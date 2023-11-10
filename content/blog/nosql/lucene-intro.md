---
title: "Lucene 简介"
linkTitle: "Lucene 简介"
date: 2022-04-29
weight: 8
---

## 简介

Apache Lucene是一个开源的高性能、可扩展的信息检索引擎，提供了强大的数据检索能力

- Scalable, High-Performance Indexing
- Powerful, Accurate and Efficient Search Algorithms

### 概念

#### Index（索引）

类似数据库的表的概念，但是没有Scheme，相当于Document的集合

#### Document（文档）

类似数据库内的行或者文档数据库内的文档的概念，写入Index的Document会被分配一个唯一的ID，即Sequence Number（更多被叫做DocId）

#### Field（字段）

一个Document会由一个或多个Field组成，Field是Lucene中数据索引的最小定义单位。Lucene提供多种不同类型的Field，例如StringField、TextField、LongFiled或NumericDocValuesField等

#### Term和Term Dictionary

Lucene中索引和搜索的最小单位，一个Field会由一个或多个Term组成，Term是由Field经过Analyzer（分词）产生。Term Dictionary即Term词典，是根据条件查找Term的基本索引

#### Segment

一个Index会由一个或多个sub-index构成，sub-index被称为Segment。Lucene的Segment设计思想，与LSM类似但又有些不同，继承了LSM中数据写入的优点，但是在查询上只能提供近实时而非实时查询

Lucene中的数据写入会先写内存的一个Buffer（类似LSM的MemTable，但是不可读），当Buffer内数据到一定量后会被flush成一个Segment，每个Segment有自己独立的索引，可独立被查询，但数据永远不能被更改。删除时，由另外一个文件保存需要被删除的文档的DocID。Index的查询需要对多个Segment进行查询并对结果进行合并，还需要处理被删除的文档，为了对查询进行优化，Lucene会有策略对多个Segment进行合并，这点与LSM对SSTable的Merge类似

Segment在被flush或commit之前，数据保存在内存中，是不可被搜索的。原因是Lucene中数据搜索依赖构建的索引（例如倒排依赖Term Dictionary），Lucene中对数据索引的构建会在Segment flush时，而非实时构建，目的是为了构建最高效索引

#### Sequence Number

DocId（即Sequence Number）实际上并不在Index内唯一，而是Segment内唯一，取值从0开始递增，且不一定连续，如果有Doc被删除，那可能会存在空洞。一个文档对应的DocId可能会发生变化，主要是发生在Segment合并时

## Field（字段）

```java
class Field {
   protected final IndexableFieldType type;
   protected final String name;
   protected Object fieldsData;
}
```

```java
interface IndexableFieldType {
   boolean stored(); // 是否需要保存该字段
   boolean tokenized(); // 是否做分词，针对TextField
   boolean storeTermVectors(); // 是否储存Term Vector，对于长度较小的字段不建议开启
   boolean omitNorms(); // 允许每个文档的每个字段都存储一个normalization factor
   IndexOptions indexOptions();
   DocValuesType docValuesType();
   int pointDimensionCount(); // 多维数据的索引，一般经纬度数据会采取这个索引方式
   int pointIndexDimensionCount();
   int pointNumBytes();
   int vectorDimension();
}
```

**`Term Vector`**

保存了一个文档内所有的term的相关信息，包括Term值、出现次数（frequencies）以及位置（positions）等，是一个per-document inverted index，提供了根据docid来查找该文档内所有term信息的能力。Term Vector的用途主要有两个，一是关键词高亮，二是做文档间的相似度匹配（more-like-this）

**`Normalization Factor`**

和搜索时的相关性计算有关的一个系数。Norms存储占一个字节，每个文档的每个字段都会独立存储一份，且Norms数据会全部加载到内存。若关闭了Norms，则无法做index-time boosting以及length normalization

**`IndexOptions`**

倒排索引的5种可选参数，用于选择该字段是否需要被索引，以及索引哪些内容

```java
enum IndexOptions {
    NONE,
    DOCS,
    DOCS_AND_FREQS,
    DOCS_AND_FREQS_AND_POSITIONS,DOCS_AND_FREQS_AND_POSITIONS_AND_OFFSETS
}
```

**`DocValue`**

正向索引（docid到field的一个列存）

```java
enum DocValuesType {
    NONE,
    NUMERIC,
    BINARY,
    SORTED,
    SORTED_NUMERIC,
    SORTED_SET
}
```



## 数据结构

![alt](/img/e6f7e6d5aa124341b222f8b068971187.png)

### FST

Lucene使用FST数据结构来存储Term Dict Index

有向无环图，在范围，前缀搜索以及压缩率上都有明显的优势

### BKDTree
