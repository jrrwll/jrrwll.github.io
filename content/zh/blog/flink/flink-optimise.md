---
title: "Flink 调优"
linkTitle: "Flink 调优"
date: 2022-04-29
weight: 3
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

