
---
title: "Flink Principle"
---

## 整体架构

Flink系统由`Flink Program`、`JobManager`、`TaskManager`三个部分组成。这三部分之间都使用Akka框架(`Actor System`)进行通信, 通过发送消息驱动任务的推进。

`Flink Program` 加载用户提交的任务代码，解析并生成任务执行拓扑图，并将拓扑图提交给`JobManager`。

`JobManager`基于任务执行拓扑图，生成相应的物理执行计划，将执行计划发送给`TaskManager`执行。除此之外，`JobManager`还负责协调`checkpoint`的生成，不断地从`TaskManager`收集Operator的状态，并周期性生成`checkpoint`，以便在系统出错时从`checkpoint`恢复之前的状态。

`TaskManager`负责管理任务执行所需的资源，执行具体的任务，并将任务产出的数据流传入给下一个任务。每个`TaskManger`上运行一个jvm进程。每个`TaskSlot`运行一个线程


## 一致性保证与出错处理

分布式场景中，数据会丢失、会乱序、会重复。乱序的问题，结合`Event Time` (表征事件发生的时间)与 `Watermark`(表征何时数据已经完整的标识)解决。针对丢失和重复的问题，Flink通过分布式快照(`distributed snapshot`),支持了`Exactly Once`的一致性语义

- ### 分布式快照

Flink基于`Chandy and Lamport`算法,实现分布式快照机制

在正常的数据流中，Flink会周期性插入一种特殊的数据记录 - `barrier`，当算子处理到`barrier`的时候，会保存算子当前的状态到持久性存储。当算子包含多个输入的时候，需要对齐多个`barrier`(align barriers)。当算子某个输入率先接收到`barrier`的时候，会缓存该输入的后续数据，直到所有的输入都收到`barrier`之后，才会触发状态备份操作，并输出`barrier`到下游算子。

除了备份各个算子的状态生成snapshot之外，对于sink还需要执行一步额外操作 —— 将结果写入外部设备。Flink通过两阶段提交的机制(2PC,two-phase commit), 来实现这个分布式事务。


## 流量控制

下游的`InputChannel`从上游的`ResultPartition`接收数据的时候，会基于当前已经缓存的数据量，以及可申请到的`LocalBufferPool`与`NetworkBufferPool`，计算出一个`Credit`值返回给上游。上游基于`Credit`的值，来决定发送多少数据。`Credit`就像信用卡额度一样，不能超支。当下游发生数据拥塞时，`Credit`减少值为0，于是上游停止数据发送。拥塞压力不断向上游传导，形成反压。
