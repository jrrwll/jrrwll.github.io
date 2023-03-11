---
title: "Redis 简介"
linkTitle: "Redis 简介"
date: 2022-05-10
weight: 10
---

## 概述
> Remote Dictionary Server

Redis是一种支持key-value等多种数据结构的存储系统。可用于缓存，事件发布或订阅，高速队列等场景。支持网络，提供字符串，哈希，列表，队列，集合结构直接存取，基于内存，可持久化

### 数据类型

https://redis.io/docs/manual/data-types/

限制：值的长度限制为512MB，元素大小限制为2^32-1

#### `string`
> https://redis.io/commands/?group=string
> incr, decr, incrby
> append, getrange, setrange
> setbit, getbit

#### `list`
> lpush, ltrim, blpop

#### `set`
> spop, srandmember, sinter

#### `zset`
> zrange, zrank, zrangebyscore

#### `hash`

#### `stream`
> https://redis.io/docs/manual/data-types/streams/#consumer-groups
> xadd, xrange, xrevrange, xread, xgroup, 

Streams 是一个只追加的数据结构

**序号范围扫描，id定位的复杂度O(logN)**
```sh
# key: mystream
# stream id: * 或手工指定
# * 自动生成<millisecondsTime>-<sequenceNumber>
# sensor-id 1234 temperature 19.8 空格分隔为列表
xadd mystream * sensor-id 1234 temperature 19.8
xadd somestream 0-* baz qux

# 最小id - 和最大id +之间的所有数据
xrange mystream - + count 10
# 时间戳范围
xrange mystream <millisecondsTime_start> <millisecondsTime_end>
# (id1 表示id刚好比id1大的数据，可用于迭代扫描
xrange mystream (id1 + count 10
# 反序
xrevrange + - count 1
```

**监听流**
```sh
# 非阻塞地从多个流中获取id大于指定id的数据
xread [count 2] streams mystream1 mystream2 id_or_time1 id_or_time2
# block 0 阻塞0ms 即永久阻塞
# $表示最后一条数据
xread block 0 streams mystream $
```

**消费组**
```sh
# 创建一个消费组，并指定消费偏移量
# $ 最后一条消息 0 最早一条消息
xgroup create mystream mygroup $
# 不存在则创建流
xgroup create newstream mygroup $ mkstream
# > 特殊ID，指到目前为止，消息从未传递给同组的其他消费者，如果指定0之类的真实ID，则表示往后没有被xack的消息
xreadgroup group mygroup consumer_name count 1 streams mystream >
```

## 流程

线程模型核心是基于非阻塞的IO多路复用机制

## 持久化

### RDB

把当前内存数据生成快照保存到硬盘

#### 手动触发

手动触发对应save命令，会阻塞当前Redis服务器，直到RDB过程完成为止

#### 自动触发

自动触发对应bgsave命令，Redis进程执行fork操作创建子进程，RDB持久化过程由子进程负责，完成后自动结束。阻塞只发生在fork阶段

`save <seconds> <changes>`，表示xx秒内数据修改xx次时自动触发bgsave

- 从节点执行全量复制操作，主节点自动执行bgsave生成RDB文件并发送给从节点
- 默认情况下执行shutdown命令时，如果没有开启AOF持久化功能则 自动执行bgsave

### AOF

以独立日志的方式记录每次写命令， 重启时再重新执行AOF文件中的命令达到恢复数据的目的

`appendonly yes` 开启AOF
`appendfilename appendonly.aof` AOF文件名

1. 所有的写入命令会追加到aof_buf（缓冲区）中
2. AOF缓冲区根据对应的策略向硬盘做同步操作(fsync 策略：无fsync、每秒fsync 、每写fsync, 默认每秒)
3. 定期对AOF文件进行重写，达到压缩的目的
4. 当Redis服务器重启时，加载AOF文件进行数据恢复

#### AOF重写

- 手动触发：`bgrewriteaof`
- 自动触发
`auto-aof-rewrite-min-size`：运行AOF重写时文件最小体积，默认为64MB
`auto-aof-rewrite-percentage`：当前AOF文件空间 `aof_current_size`和上一次重写后AOF文件空间`aof_base_size`的最小比值

`info persistence`可查看`aof_current_size`和`aof_base_size`，以上两个条件同时满足即触发AOF重写

