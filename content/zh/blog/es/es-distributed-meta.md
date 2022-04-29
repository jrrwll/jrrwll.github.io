---
title: "Elasticsearch 分布式原理Meta篇"
linkTitle: "ES 分布式原理Meta篇"
date: 2022-04-28
weight: 6
---

## `Meta`数据

Master节点管理Meta数据并通知其他节点，来驱动各个模块工作，比如创建Shard等

Meta是用来描述数据的数据。在ES中，Index的mapping结构、配置、持久化状态等就属于meta数据，集群的一些配置信息也属于meta。这类meta数据非常重要，假如记录某个index的meta数据丢失了，那么集群就认为这个index不再存在了。ES中的meta数据只能由master进行更新

### `Meta`数据结构

集群中的每个节点都会在内存中维护一个当前的ClusterState，表示当前集群的各种状态

**ClusterState**
```java
class ClusterState {
    long version; // 当前版本号，每次更新加1
    String stateUUID; // 该state对应的唯一id
    RoutingTable routingTable; // 所有index的路由表
    DiscoveryNodes nodes; // 当前集群节点
    MetaData metaData; // 集群的meta数据，需要持久化
    ClusterBlocks blocks; // 用于屏蔽某些操作
    ImmutableOpenMap<String, Custom> customs; // 自定义配置
    ClusterName clusterName; // 集群名
}
```

**MetaData**
```java
class MetaData {
    String clusterUUID; // 集群的唯一id
    long version; // 当前版本号，每次更新加1
    Settings persistentSettings; // 持久化的集群设置
    ImmutableOpenMap<String, IndexMetaData> indices; // 所有Index的Meta
    ImmutableOpenMap<String, IndexTemplateMetaData> templates; // 所有模版的Meta
    ImmutableOpenMap<String, Custom> customs; // 自定义配置
}
```

**IndexMetaData**
```java
class IndexMetaData {
    long version; // 当前版本号，每次更新加1
    int routingNumShards:; // 用于routing的shard数, 只能是该Index的numberOfShards的倍数，用于split
    State state; // Index的状态, OPEN或CLOSE
    Settings settings; // numbersOfShards，numbersOfRepilicas等配置
    ImmutableOpenMap<String, MappingMetaData> mappings; // Index的mapping
    ImmutableOpenMap<String, Custom> customs; // 自定义配置。
    ImmutableOpenMap<String, AliasMetaData> aliases; // 别名
    long[] primaryTerms; // primaryTerm在每次Shard切换Primary时加1，用于保序
    ImmutableOpenIntMap<Set<String>> inSyncAllocationIds; // 处于InSync状态的AllocationId，用于保证数据一致性
}
```

### Meta的存储

位于节点的data目录

#### `nodes/$node_id/_state/`

`global-1.st` 存储MetaData中除去IndexMetaData的部分

`node-0.st` 存储NodeId

#### `nodes/$node_id/indices/$index_id/_state/`

`state-2.st` 存储IndexMetaData

#### `nodes/$node_id/indices/$index_id/$shard_id/_state/`

`state-0.st` 存储ShardStateMetaData，包含是否是primary和allocationId等信息


### Meta的恢复

当Master进程决定进行恢复Meta时，它会向集群中的MasterNode和DataNode请求其机器上的MetaData。对于集群的Meta，选择其中version最大的版本。对于每个Index的Meta，也选择其中最大的版本。然后将集群的Meta和每个Index的Meta再组合起来，构成当前的最新Meta

## ClusterState commit的一致性

`MasterService`串行处理ClusterState的变更

### 有缺陷的两阶段提交

把Master发布ClusterState分成两步，第一步是向所有节点send最新的ClusterState，当有超过半数的master节点返回ack时，再发送commit请求，要求节点commit接收到的ClusterState。如果没有超过半数的节点返回ack，那么认为本次发布失败，同时退出master状态，执行rejoin重新加入集群

**一致性问题**：如果master在commit阶段，只commit了少数几个节点就出现了网络分区，将master与这几个少数节点分在了一起，其他节点可以互相访问。此时其他节点构成多数派，会选举出新的master，由于这部分节点中没有任何节点commit了新的ClusterState，所以新的master仍会使用更新前的ClusterState，造成Meta不一致

### 解决方案

#### 实现一个标准的一致性算法，比如raft

raft算法中，follower接收到日志后就会进行持久化，写到磁盘上。ES中，节点接收到ClusterState只是放到内存中的一个队列中即返回，并不持久化

#### 借助额外的组件保证meta一致性

用Zookeeper来保证Meta的一致性

#### 使用共享存储来保存Meta

首先保证不会出现脑裂，然后可以使用共享存储来保存Meta
