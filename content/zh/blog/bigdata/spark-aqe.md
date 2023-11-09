---
title: "Spark AQE 自适应查询执行"
linkTitle: "Spark AQE 自适应查询执行"
date: 2023-11-06
weight: 5
---

## SkewedJoin

假设A表和B表做inner join，并且A表中第0个partition是一个倾斜的partition，使用N个任务去处理该partition。每个任务只读取若干个map的shuffle 输出文件，然后读取B表partition 0的数据做join。最后，将N个任务join的结果通过Union操作合并起来。在这样的处理中，B表的partition 0会被读取N次，虽然这增加了一定的额外代价，但是通过N个任务处理倾斜数据带来的收益仍然大于这样的代价。如果B表中partition 0也发生倾斜，对于inner join也可以将B表的partition 0分成若干块，分别与A表的partition 0进行join，最终union起来。

**生效条件**

- Join物理算子类型：只针对SortMergeJoin 和 ShuffleHashJoin。对于 BroadcastHashJoin 无法生效
- 不支持outer join，对于left join不支持right表的数据倾斜优化
- 取决于Join Pattern
- - Normal Join：SortMergeJoin且两边都是Sort + Exchange的组合
- - JoinWithAgg: SortMergeJoin且一边有Agg，则该侧不支持
- - MultipleJoin：SortMergeJoin且一边有Join，则依赖该侧是否支持数据倾斜优化
- - MultipleJoinWithAggOrWin：SortMergeJoin且一边有HashAgg，则该侧不支持
- - SkewedJoinWithUnion：两个SortMergeJoin的Union，则不支持
