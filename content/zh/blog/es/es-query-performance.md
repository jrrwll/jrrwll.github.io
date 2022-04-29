---
title: "Elasticsearch 查询性能"
linkTitle: "ES 查询性能"
date: 2022-04-27
weight: 4
---

## Lucene查询原理

### Lucene的数据结构

- #### FST
保存term字典，可以在FST上实现单Term、Term范围、Term前缀和通配符查询等

对字符串范围/前缀/通配符查询，Lucene会从FST中获取到符合条件的所有Term，然后就可以根据这些Term再查找倒排链，找到符合条件的doc。FST相关的字符串查询要比倒排链查询慢很多(以上10倍)

- #### 倒排链
保存了每个term对应的docId的列表，采用skipList的结构保存，用于快速跳跃

对单个词条进行查询，Lucene会读取该词条的倒排链，倒排链中是一个有序的docId列表。单个倒排链扫描的性能在每秒千万级

- #### BKD-Tree
BKD-Tree是一种保存多维空间点的数据结构，用于数值类型(包括空间点)的快速查找

对数字类型进行范围查找，Lucene会通过BKD-Tree找到符合条件的docId集合，但这个集合中的docId并非有序的。和其他条件做交集的时候，需要构建有序的docID数组或BitSet

- #### DocValues
基于docId的列式存储，由于列式存储的特点，可以有效提升排序聚合的性能


## IndexSorting

IndexSorting是一种预排序，即数据预先按照某种方式进行排序，它是Index的一个设置，这里数据是**在每个Segment内有序**。一个Segment中的每个文档，都会被分配一个docID，docID从0开始，顺序分配。在没有IndexSorting时，docID是按照文档写入的顺序进行分配的，在设置了IndexSorting之后，docID的顺序就与IndexSorting的顺序一致

### 优化查询性能

#### 提前中断

查询的Sort顺序与IndexSorting的顺序相同，并且不需要获取符合条件的记录总数(TotalHits)时，这时候可以提前中断查询

#### 提高数据压缩率

不管行存(Store)与列存(DocValues)的存储方式，应用IndexSorting后，相邻数据的相似度就会越高，也就越利于压缩

#### 降低写入性能

### 实现原理

#### Flush时保证Segment内数据有序

数据写入Lucene后，并不是立即可查的，要生成Segment之后才能被查到。为了保证近实时的查询，ES会每隔一秒进行一次Refresh，Refresh就会调用到Lucene的Flush生成新的Segment

每个doc写入进来之后，按照写入顺序被分配一个docID，然后被IndexingChain处理，依次要对invert index、store fields、doc values和point values进行处理，有些数据会直接写到文件里，主要是store field和term vector，其他的数据会放到memory buffer中

在Flush时，首先根据设定的列排序，这个排序可以利用内存中的doc values，排序之后得到老的docID到新docID的映射，因为之前docID是按照写入顺序生成的，现在重排后，生成的是新的排列。对于已经写到文件中的数据，比如store field和term vector，需要从文件中读出来，重新排列后再写到一个新文件里，原来的文件就相当于一个临时文件。对于内存中的数据结构，直接在内存中重排后写到文件中。

相比没有IndexSorting时，对性能影响比较大的一块就是store field的重排，因为这部分需要从文件中读出再写回，而其他部分都是内存操作，性能影响稍小一些

#### Merge时保证新的Segment数据有序

由于Flush时Segment已经是有序的了，所以在Merge时也就可以采用非常高效的Merge Sort的方式进行
