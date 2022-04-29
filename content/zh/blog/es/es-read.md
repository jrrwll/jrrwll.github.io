---
title: "ElasticSearch 查询"
linkTitle: "ES 查询"
date: 2022-02-17
weight: 2
---

## 查询流程

### Lucene的读

- **`TopDocs search(Query query, int n)`** 返回最满足Query的N个结果
- **`Document doc(int docID)`** 通过doc id查询Doc内容
- **`int count(Query query)`** 通过Query获取到命中数

Search类实时（Near Real Time）请求：同时查询内存和磁盘上的Segment，最后将结果合并后返回。所有的搜索系统一般都是两阶段查询，第一阶段查询到匹配的DocID，第二阶段再查询DocID对应的完整文档，在Elasticsearch中称为`query_then_fetch`。而一阶段查询`query_and_fetch`，适用于查询一个Shard的请求。根据TF（Term Frequency）和DF（Document Frequency）来算分的查询使用三阶段查询，即先收集所有Shard中的TF和DF值，然后将这些值带入请求中，再次执行query_then_fetch，这样算分的时候TF和DF就是准确的，称作`DFS_query_then_fetch`，另有`DFS_query_and_fetch`。另一种选择是用BM25代替TF/DF模型

Get类实时（Real Time）请求：先查询内存中的TransLog，没找到则再查询磁盘上的TransLog，还没有则再查询磁盘上的Segment。这种查询顺序可以保证查询到最新版本的Doc

### Client Node

1. Get Remove Cluster Shard
   判断是否需要跨集群访问，如果需要，则获取到要访问的Shard列表

2. Get Search Shard Iterator
   获取当前Cluster中要访问的Shard，和上一步中的Remove Cluster Shard合并，构建出最终要访问的完整Shard列表。
   这一步中，会在Primary Node和多个Replica Node中选择出一个要访问的Shard

3. For Every Shard:Perform
   遍历每个Shard，对每个Shard执行后面逻辑

4. Send Request To Query Shard
   将查询阶段请求发送给相应的Shard

5. Merge Docs
   上一步将请求发送给多个Shard后，这一步就是异步等待返回结果，然后对结果合并。这里的合并策略是维护一个Top N大小的优先级队列，每当收到一个shard的返回，就把结果放入优先级队列做一次排序，直到所有的Shard都返回。

6. Send Request To Fetch Shard
   选出Top N个Doc ID后发送Fetch Shard给这些Doc ID所在的Shard，最后会返回Top N的Doc的内容。

### Query Phase

1. Create Search Context
   创建Search Context，之后Search过程中的所有中间状态都会存在Context中

2. Parse Query
   解析Query的Source，将结果存入Search Context

3. Get From Cache
   判断请求是否允许被Cache，如果允许，则检查Cache中是否已经有结果，如果有则直接读取Cache，如果没有则继续执行后续步骤，执行完后，再将结果加入Cache

4. Add Collectors
   Collector主要目标是收集查询结果，实现排序，自定义结果集过滤和收集等。这一步会增加多个Collectors，多个Collector组成一个List

5. Lucene::search
   调用Lucene中IndexSearch的search接口，执行真正的搜索逻辑。每个Shard中会有多个Segment，每个Segment对应一个LeafReaderContext，这里会遍历每个Segment，到每个Segment中去Search结果，然后计算分数

6. Rescore
   根据Request中是否包含rescore配置决定是否进行二阶段排序

7. Suggest::execute
   如果有推荐请求，则在这里执行推荐请求。

8. Aggregation::execute
   如果含有聚合统计请求，则在这里执行。Elasticsearch中的aggregate的处理逻辑也类似于Search，通过多个Collector来实现。在Client  Node中也需要对aggregation做合并

### Fetch Phase

通过DocID获取到用户需要的完整Doc内容。这些内容包括了DocValues，Store，Source，Script和Highlight等
