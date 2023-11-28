---
title: "Flink 优化技巧"
linkTitle: "Flink 优化技巧"
date: 2022-04-29
---

## Group Aggregate

### 开启 MicroBatch/MiniBatch （牺牲延迟以提升吞吐）

缓存一定的数据后再触发处理，以减少对 state 的访问从而显著提升吞吐，以及减少输出数据量

MiniBatch主要依靠在每个 task 上注册的 timer 线程来触发微批，会有一定的线程调度开销。MicroBatch 是 MiniBatch 的升级版，主要基于事件消息来触发微批，事件消息会按用户指定的时间间隔在源头插入。MicroBatch 在攒批效率、反压表现、吞吐和延迟性能上都要胜于MiniBatch

### 开启 LocalGlobal （解决常见数据热点问题）
> 适用于提升如 SUM, COUNT, MAX, MIN, AVG 等普通 agg 上的性能，以及解决这些场景下的数据热点问题

将原先的 Aggregate 分成Local+Global 两阶段聚合。第一阶段在上游节点本地攒一批数据进行聚合（localAgg），并输出这次微批的增量值（Accumulator），第二阶段再将收到的 Accumulator merge起来，得到最终的结果（globalAgg）

## TopN 

分组 TopN：根据不同的分组进行排序，计算出每个分组的一个排行榜

```sql
SELECT *
FROM (
  SELECT *,
    ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]]
      ORDER BY col1 [asc|desc][, col2 [asc|desc]...]) AS rownum
  FROM table_name)
WHERE rownum <= N [AND conditions]
```

最优的流式 TopN 的计算只需要维护一个 N 元素大小的小根堆，每当有数据到达时，只需要与堆顶元素比较，如果比堆顶元素还小，则直接丢弃；如果比堆顶元素大，则更新小根堆，并输出更新后的排行榜

TopN 算子的实现上主要有两个数据结构，一个是 TreeMap，另一个是 MapState。TreeMap作为小根堆，有序地存放了排名前 N 的元素。MapState用于持久化TreeMap

### 嵌套 TopN 解决热点问题
> 数据倾斜(最坏的情况是全局 TopN，所有数据都倾斜到一个节点)

两层 TopN。在原先的 TopN 前面，再加一层 TopN，用于分散热点

```sql
CREATE VIEW tmp_topn AS
SELECT *
FROM (
  SELECT *,
    ROW_NUMBER() OVER (PARTITION BY HASH_CODE(shop_id)%128 ORDER BY sales DESC) AS rownum
  FROM shop_sales)
WHERE rownum <= 10

SELECT *
FROM (
  SELECT shop_id, shop_name, sales,
    ROW_NUMBER() OVER (ORDER BY sales DESC) AS rownum
  FROM tmp_topn)
WHERE rownum <= 10
```


## CPU 优化

- json解析加速 json-iterator

```java
Config newConfig = JsoniterSpi.getDefaultConfig().copyBuilder().
encodingMode(EncodingMode.DYNAMIC_MODE).
decodingMode(DecodingMode.DYNAMIC_MODE_AND_MATCH_FIELD_WITH_HASH).
escapeUnicode(false).
build();
JsoniterSpi.setDefaultConfig(newConfig);
```

- kafak schema 使用 ByteArraySchema

- enableObjectReuse
使用StringValue、LongValue等可变对象。被Flink Gelly普遍使用

- 使用tuple而非map

- join策略

```java
// ds1比ds2小得多的时候使用，将ds1的同分区key的数据，全数发给ds2所在节点上
ds1.join(ds2, JoinHint.BROADCAST_HASH_FIRST)
```

## Failover Strategy 优化

**策略对比**
RestartAllStrategry： 任何一个Task失败则会重启整个Job
RestartPipelinedRegionStrategy：Task级别重启且无限重试，streaming场景下

**现存问题**

- 任务失败，上游DataSource不支持数据重发，导致Task重启后缺数据
- 目前RestartPipelinedRegionStrategy是为Streaming设计，没有重启上限
- 部分OutPlugin不支持Task重启，例如Hive->MySQL/Tableau任务运行一半失败后，已经有部分数据的写入，由于每个task写入的数据是随机的，重启时也难以提前清除上次写入的数据。

**解决思路**
- 保存每个Task启动时获取的DataSource Split信息，如果任务失败，Task会回退Split信息,上游Task重启后支持数据重发
- 在RestartPipelinedRegionStrategy基础上，实现BatchJobFailoverStrategy,并支持Region Task重启次数配置，重启次数超过上限任务会失败
- 针对下游不可重发的Plugin，不支持Task级别重启，直接使用Job级别重启
