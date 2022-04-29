---
title: "Elasticsearch 分布式原理Node篇"
linkTitle: "ES 分布式原理Node篇"
date: 2022-04-27
weight: 5
---

## ES集群构成

配置文件 `conf/elasticsearch.yml`

```yaml
node.master: true
node.data: false
```

当node.master为true时，其表示这个node是一个master的候选节点，可以参与选举，在ES的文档中常被称作 master-eligible node，类似于Leader和Candidate的关系。当node.data为true时，这个节点作为一个数据节点，会存储分配在该node上的shard的数据并负责这些shard的写入、查询等。此外，任何一个集群内的node都可以执行任何请求，其会负责将请求转发给对应的node进行处理

## 节点发现

> ES ZenDiscovery在选举时没有term的概念，不能保证每轮每个节点只投一票

Node启动后，首先要通过节点发现功能加入集群。[ZenDiscovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-hosts-providers.html?spm=ata.21736010.0.0.3ff518f1oBkJmL)是ES自己实现的一套用于节点发现和选主等功能的模块

```yaml
# 推荐这里设置为所有的master-eligible node
discovery.zen.ping.unicast.hosts: [x.x.x.1, x.x.x.2， x.x.x.3]
```

## Master选举

保证选举出的master被多数派(quorum)的master-eligible node认可，以此来保证只有一个master

```yaml
# 设置 quorum
discovery.zen.minimum_master_nodes: 2
```

### master选举发起的时机

当一个节点发现包括自己在内的多数派的master-eligible节点认为集群没有master时，就可以发起master选举

- 该master-eligible节点的状态为 `当前没有master`
- 该master-eligible节点通过ZenDiscovery模块的ping操作询问其已知的集群其他节点的信息，获取到的状态都为 `当前没有master`
- 包括本节点在内，当前已有超过minimum_master_nodes个节点认为集群没有master

### master选举的投票对象

当clusterStateVersion越大，优先级越高。这是为了保证新Master拥有最新的clusterState(即集群的meta)，避免已经commit的meta变更丢失

```java
class Some {
    // 计算出选举的投票对象
    public MasterCandidate electMaster(Collection<MasterCandidate> candidates) {
        assert hasEnoughCandidates(candidates);
        List<MasterCandidate> sortedCandidates = new ArrayList<>(candidates);
        sortedCandidates.sort(MasterCandidate::compare);
        return sortedCandidates.get(0);
    }

    // 先根据节点的clusterStateVersion比较，相同时，则按照节点的Id比较(Id为节点第一次启动时随机生成)
    public static int compare(MasterCandidate c1, MasterCandidate c2) {
        int ret = Long.compare(c2.clusterStateVersion, c1.clusterStateVersion);
        if (ret == 0) {
            ret = compareNodes(c1.getNode(), c2.getNode());
        }
        return ret;
    }
}    
```

### 达成选举

#### 假设Node_A选Node_B当Master

Node_A会向Node_B发送join请求，那么此时：

(1) 如果Node_B已经成为Master，那么Node_B就会把Node_A加入到集群中，然后发布最新的cluster_state, 最新的cluster_state就会包含Node_A的信息。相当于一次正常情况的新节点加入。对于Node_A，等新的cluster_state发布到Node_A的时候，Node_A也就完成join了

(2) 如果Node_B在竞选Master，那么Node_B会把这次join当作一张选票。对于这种情况，Node_A会等待一段时间，看Node_B是否能成为真正的Master，直到超时或者有别的Master选成功。、

(3) 如果Node_B认为自己不是Master(现在不是，将来也选不上)，那么Node_B会拒绝这次join。对于这种情况，Node_A会开启下一轮选举

#### 假设Node_A选自己当Master

此时NodeA会等别的node来join，即等待别的node的选票，当收集到超过半数的选票时，认为自己成为master，然后变更cluster_state中的master node为自己，并向集群发布这一消息

### 脑裂保证

一个节点可能在相邻的两轮选主中选择不同的主，恰好导致这两个被选的节点都成为了主。但是这种脑裂很快会自动恢复，因为不一致发生后某个master再次发布cluster_state时就会发现无法达到多数派条件，或者是发现它的follower并不构成多数派而自动降级为candidate等

## 错误检测

### `MasterFaultDetection` Master定期检测集群内其他的Node

如果Master检测到某个Node连不上了，会执行removeNode的操作，将节点从cluster_state中移除，并发布新的cluster_state。当各个模块apply新的cluster_state时，就会执行一些恢复操作，比如选择新的primaryShard或者replica，执行数据复制等

### `NodesFaultDetection` 集群内其他的Node定期检测当前集群的Master

如果某个Node发现Master连不上了，会清空pending在内存中还未commit的new cluster_state，然后发起rejoin，重新加入集群(如果达到选举条件则触发新master选举)。

### `rejoin`

Master发现自己已经不满足多数派条件(>=minimumMasterNodes)了，需要主动退出master状态(退出master状态并执行rejoin)以避免脑裂的发生

- `electMasterService.hasEnoughMasterNodes` 当有节点连不上时，会执行removeNode。在执行removeNode时判断剩余的Node是否满足多数派条件，如果不满足，则执行rejoin

- `publishClusterState.publish` 在publish新的cluster_state时，分为send阶段和commit阶段，send阶段要求多数派必须成功，然后再进行commit。如果在send阶段没有实现多数派返回成功，那么可能是有了新的master或者是无法连接到多数派个节点等，则master需要执行rejoin

- `otherClusterStateVersion > localClusterState.version()` 在对其他节点进行定期的ping时，发现有其他节点也是master，此时会比较本节点与另一个master节点的cluster_state的version，谁的version大谁成为master，version小的执行rejoin

## 集群扩缩容

### 扩容DataNode

rebalance

https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-reroute.html

https://www.elastic.co/guide/en/elasticsearch/reference/current/shards-allocation.html

### 缩容DataNode

https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-filtering.html

```http
PUT /_cluster/settings
{
  "transient" : {
    "cluster.routing.allocation.exclude._ip" : "10.0.0.1"
  }
}
```

### 扩容MasterNode

先增大quorum，然后扩容节点
```http
PUT /_cluster/settings 
{
    "persistent" : {
        "discovery.zen.minimum_master_nodes" : 3
    }
}'
```

### 缩容MasterNode

缩容MasterNode与扩容跟扩容是相反的流程，我们需要先把节点缩下来，再把quorum数调下来
