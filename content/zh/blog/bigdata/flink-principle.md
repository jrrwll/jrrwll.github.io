---
title: "Flink 原理"
linkTitle: "Flink 原理"
date: 2022-04-29
weight: 2
---

## 流量控制

### 基于Credit的反压机制

下游的InputChannel从上游的ResultPartition接收数据的时候，会基于当前已经缓存的数据量，以及可申请到的LocalBufferPool与NetworkBufferPool，计算出一个Credit值返回给上游。上游基于Credit的值，来决定发送多少数据。Credit就像信用卡额度一样，不能超支

当下游发生数据拥塞时，Credit减少值为0，于是上游停止数据发送。拥塞压力不断向上游传导，形成反压

## 系统容错

流计算容错一致性保证有三种，分别是：
- Exactly-once，是指每条 event 会且只会对 state 产生一次影响，这里的“一次”并非端到端的严格一次，而是指在 Flink 内部只处理一次，不包括 source和 sink 的处理
- At-least-once，是指每条 event 会对 state 产生最少一次影响，也就是存在重复处理的可能
- At-most-once，是指每条 event 会对 state 产生最多一次影响，就是状态可能会在出错时丢失

### Checkpointing检查点

Flink会在流上定期产生一个barrier（屏障）。barrier 是一个轻量的，用于标记stream顺序的数据结构。barrier被插入到数据流中，作为数据流的一部分和数据一起向下流动，过程如下：
1. barrier 由source节点发出
2. barrier会将流上event切分到不同的checkpoint中
3. 汇聚到当前节点的多流的barrier要对齐（At least once不需要对齐）
4. barrier对齐之后会进行Checkpointing，生成snapshot，快照保存到StateBackend中
5. 完成snapshot之后向下游发出barrier，继续直到Sink节点

