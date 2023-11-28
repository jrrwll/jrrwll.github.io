
---
title: "布隆过滤器"
linkTitle: "布隆过滤器"
date: 2023-11-11
---

## Hash 函数

### MurmurHash

MurmurHash 是一种非加密型哈希函数，适用于一般的哈希检索操作。由Austin Appleby在2008年发明，并出现了多个变种，都已经发布到了公有领域。与其它流行的哈希函数相比，对于规律性较强的key，MurmurHash的随机分布特征表现更良好。

特点：
- 快。MurMurHash3 比 MD5 快。
- 低碰撞。MurMurHash3 128 位版本哈希值是 128 位的，跟 MD5 一样。128 位的哈希值，在数据量只有千万级别的情况下，基本不用担心碰撞。
- 高混淆。散列值比较“均匀”，如果用于哈希表，BF 等, 元素就会均匀分布。

### FNV

FNV 哈希算法全名为 Fowler-Noll-Vo 算法，是以三位发明人Glenn Fowler，Landon Curt Noll，Phong Vo的名字来命名的，最早在1991年提出。

特点：FNV能快速 hash 大量数据并保持较小的冲突率，它的高度分散使它适用于hash一些非常相近的字符串，比如URL，hostname，文件名，text，IP地址等。

## 变种

### Counting Bloom Filter

传统的 BF 并不支持删除操作。但是名为 Counting Bloom Filter 的变种可以用来测试元素计数个数是否绝对小于某个阈值，它支持元素删除。

它将标准 Bloom Filter 位数组的每一位扩展为一个小的计数器（Counter），在插入元素时给对应的 k （k 为哈希函数个数）个 Counter 的值分别加 1，删除元素时给对应的 k 个 Counter 的值分别减 1。Counting Bloom Filter 通过多占用几倍的存储空间的代价， 给 Bloom Filter 增加了删除操作。
CBF 虽说解决了 BF 的不能删除元素的问题，但是自身仍有不少的缺陷有待完善，比如 Counter 的引入就会带来很大的资源浪费， 因此在实际的使用场景中会有很多 CBF 的升级版。

### Spectral Bloom Filter

SBF（Spectral Bloom Filter）在 CBF 的基础上提出了元素出现频率查询的概念，将CBF的应用扩展到了 multi-set 的领域。

### d-Left Counting Bloom Filter

dlCBF（d-Left Counting Bloom Filter）利用 d-left hashing 的方法存储 fingerprint，解决哈希表的负载平衡问题。

### Accurate Counting Bloom Filter

ACBF（Accurate Counting Bloom Filter）通过 offset indexing 的方式将 Counter 数组划分成多个层级，来降低误判率。

## 扩展

### Redis Bloom

Redis Bloom 是基于 Redis Module 实现的模块能力，整体是基于 Redis 协议，使用的是 Redis 的客户端。

Redis Bloom 支持 自动扩容，错误率计算，以及自动过期 等能力。

在正常 bf.add 操作后，若不存在过滤器，则会自动生成一个默认的过滤器，其中capacity和error_rate会影响整体的hash次数和存储空间，具体可以参考：https://en.wikipedia.org/wiki/Bloom_filter
