---
title: "Elasticsearch 分布式原理Data篇"
linkTitle: "ES 分布式原理Data篇"
date: 2022-04-28
weight: 7
---

## Replication写入流程

ES中每个Index会划分为多个Shard，Shard分布在不同的Node上，以此来实现分布式的存储和查询，支撑大规模的数据集。对于每个Shard，又会有多个Shard的副本，其中一个为Primary，其余的一个或多个为Replica。数据在写入时，会先写入Primary，由Primary将数据再同步给Replica。在读取时，为了提高读取能力，Primary和Replica都会接受读请求

##### 1. 检查Active的Shard数

`wait_for_active_shards` 在每次写入前，该shard至少具有的active副本数

##### 2. 写入Primary
##### 3. 并发的向所有Replicate发起写入请求
##### 4. 等所有Replicate返回或者失败后，返回给Client

如果Replica写入失败，ES会执行一些重试逻辑等，但最终并不强求一定要在多少个节点写入成功

如果一个Replica写失败了，Primary会将这个信息报告给Master，然后Master会在Meta中更新这个Index的InSyncAllocations配置，将这个Replica从中移除，移除后它就不再承担读请求

## PacificA算法

一种用于日志复制系统的分布式一致性算法

https://www.microsoft.com/en-us/research/publication/pacifica-replication-in-log-based-distributed-storage-systems/?spm=ata.21736010.0.0.79aa17cdOVS8FP

- 强一致性
- 单Primary向多Secondary的数据同步模式
- 使用额外的一致性组件Configuration Manager维护Configuration
- 少数派Replica可用时仍可写入

### 名词解释

##### Replica Group

一个互为副本的数据集合叫做Replica Group，每个副本是一个Replica。一个Replica Group中只有一个副本是Primary，其余为Secondary

##### Configuration

一个Replica Group的Configuration描述了这个Replica Group包含哪些副本，其中Primary是谁等

##### Configuration Version

Configuration的版本号，每次Configuration发生变更时加1

##### Configuration Manager

管理Configuration的全局组件，其保证Configuration数据的一致性。Configuration变更会由某个Replica发起，带着Version发送给Configuration Manager，Configuration Manager会检查Version是否正确，如果不正确则拒绝更改

##### Query & Update

对一个Replica Group的操作分为两种，Query和Update，Query不会改变数据，Update会更改数据

##### Serial Number sn

代表每个Update操作执行的顺序，每次Update操作加1，为连续的数字

##### Prepared List

Update操作的准备序列

##### Committed List

Update操作的提交序列，提交序列中的操作一定不会丢失(除非全部副本挂掉)。在同一个Replica上，Committed List一定是Prepared List的前缀

### `Primary Invariant`

> 任何时候，当一个Replica认为自己是Primary时，Configuration Manager中维护的Configuration也认为其是当前的Primary。任何时候，最多只有一个Replica认为自己是这个Replica Group的Primary

如果不能满足Primary Invariant，那么Query请求就可能发送给Old Primary，读到旧的数据

Primary会定期获取一个Lease，获取之后认为某段时间内自己肯定是Primary，一旦超过这个时间还未获取到新的Lease就退出Primary状态。只要各个机器的CPU不出现较大的时钟漂移，那么就能够保证Lease机制的有效性

实现Lease机制的方式是，Primary定期向所有Secondary发送心跳来获取Lease，而不是所有节点都向某个中心化组件获取Lease。这样的好处是分散了压力，不会出现中心化组件故障而导致所有节点失去Lease的情况

### `Commited Invariant`

> SecondaryCommittedList一定是PrimaryCommittedList的前缀，PrimaryCommittedList一定是SecondaryPreparedList的前缀

#### Query

Query只能发送给Primary，Primary根据最新commit的数据，返回对应的值。由于算法要求满足Primary Invariant，所以Query总是能读到最新commit的数据

#### Update

1. Primary分配一个Serial Number(简称sn)给一个UpdateRequest
2. Primary将这个UpdateRequest加入自己的Prepared List，同时向所有Secondary发送Prepare请求，要求将这个UpdateRequest加入Prepared List
3. 当所有Replica都完成了Prepare，即所有Replica的Prepared List中都包含了该Update请求时，Primary开始Commit这个请求，即将这个UpdateRequest放入Committed List中，同时Apply这个Update。因为同一个Replica上，Committed List永远是Prepared List的前缀，所以Primary实际上是提高Committed Point，把这个Update Request包含进来
4. 返回客户端，Update操作成功

当下一次Primary向Secondary发送请求时，会带上Primary当前的Committed Point，此时Secondary才会提高自己的Committed Point

### `Reconfiguration Invariant`
> 当一个新的Primary在T时刻完成Reconciliation时，那么T时刻之前任何节点(包括原Primary)的Commited List都是新Primary当前Commited List的前缀

Reconfiguration Invariant表明了已经Commit的数据在Reconfiguration过程中不会丢

#### 1. Secondary故障

当一个Secondary故障时，Primary向Configuration Manager发起Reconfiguration，将故障节点从Replica Group中删除。一旦移除这个Replica，它就不属于这个Replica Group了，所有请求都不会再发给它

假设某个Primary和Secondary发生了网络分区，但是都可以连接Configuration Manager。这时候Primary会检测到Secondary没有响应了，Secondary也会检测到Primary没有响应。此时两者都会试图发起Reconfiguration，将对方从Replica Group中移除，这里的策略是First Win的原则，谁先到Configuration Manager中更改成功，谁就留在Replica Group里，而另外一个已经不属于Replica Group了，也就无法再更新Configuration了。由于Primary会向Secondary请求一个Lease，在Lease有效期内Secondary不会执行Reconfiguration，而Primary的探测间隔必然是小于Lease时间的

#### 2. Primary故障

当一个Primary故障时，Secondary会收不到Primary的心跳，如果超过Lease的时间，那么Secondary就会发起Reconfiguration，将Primary剔除，这里也是First Win的原则，哪个Secondary先成功，就会变成Primary

当一个Secondary变成Primary后，需要先经过一个叫做Reconciliation的阶段才能提供服务。由于上述的Commited Invariant，所以原先的Primary的Committed List一定是新的Primary的Prepared List的前缀，那么我们将新的Primary的Prepared List中的内容与当前Replica Group中的其他节点对齐，相当于把该节点上未Commit的记录在所有节点上再Commit一次，那么就一定包含之前所有的Commit记录

#### 3. 新加节点

新加的节点需要先成为Secondary Candidate，这时候Primary就开始向其发送Prepare请求，此时这个节点还会追之前未同步过来的记录，一旦追平，就申请成为一个Secondary，然后Primary向Configuration Manager发起配置变更，将这个节点加入Replica Group

如果一个节点曾经在Replica Group中，由于临时发生故障被移除，现在需要重新加回来。此时这个节点上的Commited List中的数据肯定是已经被Commit的了，但是Prepared List中的数据未必被Commit，所以应该将未Commit的数据移除，从Committed Point开始向Primary请求数据

## SequenceNumber、Checkpoint与故障恢复

### Term和SequenceNumber

每个写操作都会分配两个值，Term和SequenceNumber。Term在每次Primary变更时都会加1，类似于PacificA论文中的Configuration Version。SequenceNumber在每次操作后加1，类似于PacificA论文中的Serial Number。由于写请求总是发给Primary，所以Term和SequenceNumber会由Primary分配，在向Replica发送同步请求时，会带上这两个值

### LocalCheckpoint和GlobalCheckpoint

LocalCheckpoint代表本Shard中所有小于该值的请求都已经处理完毕。GlobalCheckpoint代表所有小于该值的请求在所有的Replica上都处理完毕。GlobalCheckpoint会由Primary进行维护，每个Replica会向Primary汇报自己的LocalCheckpoint，Primary根据这些信息来提升GlobalCheckpoint

GlobalCheckpoint是一个全局的安全位置，代表其前面的请求都被所有Replica正确处理了，可以应用在节点故障恢复后的数据回补。另一方面，GlobalCheckpoint也可以用于Translog的GC，因为之前的操作记录可以不保存了

### 快速故障恢复

当一个Replica故障时，ES会将其移除，当故障超过一定时间，ES会分配一个新的Replica到新的Node上，此时需要全量同步数据。但是如果之前故障的Replica回来了，就可以只回补故障之后的数据，追平后加回来即可，实现快速故障恢复。实现快速故障恢复的条件有两个，一个是能够保存故障期间所有的操作以及其顺序，另一个是能够知道从哪个点开始同步数据。第一个条件可以通过保存一定时间的Translog实现，第二个条件可以通过Checkpoint实现，所以就能够实现快速的故障恢复

### ES与PacificA的比较

- Meta一致性和Data一致性分开处理：PacificA中通过Configuration Manager维护Configuration的一致性，ES中通过Master维护Meta的一致性
- 维护同步中的副本集合：PacificA中维护Replica Group，ES中维护InSyncAllocationIds
- SequenceNumber：在PacificA和ES中，写操作都具有SequenceNumber，记录操作顺序
