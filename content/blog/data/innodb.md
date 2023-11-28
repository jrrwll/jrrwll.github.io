
---
title: "MySQL InnoDB"
---

## Insert Buffer

对非聚集索引 (**辅助索引而非主键索引，并且是不唯一的**，辅助索引一般是非聚集离散分布的，唯一性校验需要查找索引页) 的插入和更新操作，不直接插入到索引页，而是先判断插入的非聚集索引页是否在缓冲池，在，则直接插入，否则存到Insert Buffer对象中。然后后台合并Insert Buffer对象和非聚集索引页子节点

### Insert Buffer B+ tree

非叶子节点：space(table space id, 4 bytes) --- marker(1 bytes) --- offset(4 bytes)

叶子节点：space(table space id, 4 bytes) --- marker(1 bytes) --- offset(4 bytes) --- metadata(4 bytes) --- record(9 bytes)

## 关键配置

### `innodb_flush_log_at_trx_commit`

> 用于设置InnoDB 在事务提交后的日志写入频率

- 0 表示log buffer 每秒都会写入到日志文件并刷新到磁盘。这种情况下MySQL的性能最好，但如果 mysqld 进程崩溃，通常会导致最后 1秒的日志丢失
- 1 这是默认值，表示每次事务提交时，log buffer 会被写入到日志文件并刷新到磁盘。这种配置最安全，但由于每次事务都需要进行磁盘I/O，所以性能最差。
- 2 表示每次事务提交时log buffer都会写入到日志文件，但是每隔1秒才会将日志文件内容刷新到磁盘。该场景下，如果mysqld进程奔溃，不会造成日志的丢失，但如果操作系统奔溃，通常会导致最后 1秒的日志丢失。

对于一些数据一致性要求不高的场景，设置为2就行了；如果要求最高的性能则可以设置为0；像一些对数据一致性要求很严格的业务场景（如金融支付），即使性能最差，也最好设置为1保证数据没有丢失。

### `sync_binlog`

> 用于控制MySQL binlog同步到磁盘的策略

- 0: 表示MySQL依赖操作系统不定期把binlog刷新到磁盘。
- n (n>0) : 表示MySQL每完成n次事务，就调用文件系统的刷新操作将缓存刷到磁盘（如果启用了autocommit，那么每个语句都会写一次binlog，否则每个事务写一次）。

sync_binlog 设置 0可以保证最高的抗并发能力，但是数据库可能会出现阻塞（binlog日志量太大，操作系统同步磁盘时占用过多资源导致业务出现阻塞）； 如果业务要求数据库合适的抗并发能力，且不希望出现阻塞，sync_binlog 设置 n（n>1, 根据具体业务和压测结果设置）； 对于金融支付这类场景，为保证安全性，还是推荐sync_binlog设置为1。
