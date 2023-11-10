
---
title: "Hudi Intro"
---

## 表类型 Table Type

- **写时复制 Copy On Write**

支持 Snapshot Queries 快照查询、Incremental Queries 增量查询

仅使用列式文件格式（例如 parquet）存储数据。通过在写入期间执行同步合并来简单更新版本并重写文件

- **读时合并 Merge On Read**

支持快照查询、增量查询、Read Optimized Queries 读取优化查询

使用列式（例如 parquet）+基于行（例如 avro）文件格式的组合来存储数据。更新会记录到增量文件中，然后进行压缩，以同步或异步方式生成新版本的列式文件

下表总结了这两种表类型之间的权衡

| 权衡 Trade-off             | CopyOnWrite                          | MergeOnRead                                                  |
| -------------------------- | ------------------------------------ | ------------------------------------------------------------ |
| 数据延迟 Data Latency      | 更高 Higher                          | 更低 Lower                                                   |
| 查询延迟 Query Latency     | 更低 Lower                           | 更高 Higher                                                  |
| 更新成本 Update cost (I/O) | 更高，需要重写整个parquet            | 更低，追加到增量日志                                         |
| Parquet文件大小            | 更小 Smaller (high update(I/0) cost) | 更大 Larger (low update cost)                                |
| 写放大 Write Amplification | Higher                               | 较低且取决于压缩策略 Lower (depending on compaction strategy) |

## 查询类型 Query types

- **快照查询 Snapshot Queries**

查询会看到给定提交或压缩操作时表的最新快照。在读取表上合并的情况下，它通过即时合并最新文件切片的基本文件和增量文件来公开近乎实时的数据（几分钟）。对于写时复制表，它提供了现有parquet表的直接替代品，同时提供更新插入/删除和其他写入侧功能。

- **增量查询 Incremental Queries**

查询只能看到自给定提交/压缩以来写入表的新数据。这有效地提供了变更流以启用增量数据管道

- **读取优化查询 Read Optimized Queries**

查询会看到给定提交/压缩操作时表的最新快照。仅公开最新文件切片中的基础/列式文件，并保证与非 hudi 列式表相同的列式查询性能
