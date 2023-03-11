
---
title: "Redis Cli"
---

## 事务

- #### `multi` 开启事务

- #### `exec` 提交事务，执行队列中的所有命令

- #### `discard` 放弃执行事务，清空事务队列

- #### `watch key1 [key2...]` 监听某个键，如果在exec之前被更改了，则放弃执行事务

- #### `unwatch [key1...]` 取消目前对键的监视，客户端断开连接也会取消监视

## 发布与订阅

- #### `subscribe key1 [key2...]` 订阅频道，支持通配符的版本 `psubscribe`

- #### `unsubscribe key1 [key2...]` 取消订阅频道，支持通配符的版本 `punsubscribe`

- #### `publish key value [key2...]` 发布消息到频道

- #### `pubsub` 自省

## 类型操作

```shell
keys *
```

### string

```shell
set k v
# string
type k
get k
# return old value
getset k v
mset k1 v1 k2 v2
mget k1 k2

# set if not exsit
msetnx k1 v1 k2 v2
# append to the tail
append k v
# string legnth
strlen k
# increment a number string, k++
incr k
decr k
# k += d
incrby k d
decrby k d

# 覆写，从偏移量offset 开始
setrange k offset v
# 子串，含两端
getrange k start end
# 设置或清除指定偏移量上的位
setbit k offset v

setnx k v
# expire in seconds
set k v ex seconds nx
```

### list

```shell
# 将一个或多个值 value插入到列表 key 的表头
lpush k v1 v2
# push if exist
lpushx k v
# 返回列表 key 中指定区间内的元素，区间以偏移量 start=0 和 end=-1 指定
lrange key start end
# 移除并返回列表 key 的头元素
lpop k
llen k

# 从头部开始移除列表中与参数 value 相等的元素
lrem k count v
lset k index v
# 返回列表 key 中，下标为 index 的元素
lindex k index
# 修剪
ltrim k start end
# 将值 value 插入到列表 key 当中，位于值 p 之前或之后 after
linsert k before p v

# 将一个或多个值 value插入到列表 key 的表尾
rpush k v1 v2
rpushx k v
rpop

# 在一个原子时间内，执行两个动作：
# 将列表 src 中的尾元素弹出返回，并插入到列表 dest
rpoplpush src dest
```

### hash

```shell
hset k f v
# 将哈希表 key 中的域 field 的值设置为 value ，当且仅当域 field 不存在。
hsetnx k f v

hexists k f
hlen k

# 删除哈希表 key 中的一个或多个指定域，不存在的域将被忽略。
hdel k f1 f2

# ++
hincrby k f increment

hgetall k
# 返回哈希表 key 中的所有域
hkeys k
# 返回哈希表 key 中所有域的值
hvals k

# 同时将多个 field-value (域-值)对设置到哈希表 key 中
hmset k f1 v1 f2 v2
```

### set

```shell
# 已经存在于集合的 member 元素将被忽略。
sadd k m1 m2
smembers k
# 集合中元素的数量
scard k

# 判断 member 元素是否集合 key 的成员
sismember k m

# 移除并返回集合中的一个随机元素
spop k
# 返回集合中的一个随机元素
srandmember k

# 移除集合 key 中的一个或多个member 元素，不存在的 member 元素会被忽略
srem k m1 m2

# 原子性操作  将 member 元素从 source 集合移动到 destination 集合
smove src dest m


sdiff set1 set2
sdiffstore diffSet set1 set2
sunion set1 set2
sinterstore interSet set1 set2
```

### zset

```shell
zadd k score1 m1 score2 m2 
zcard k
zincrby k increment m

# 返回有序集 key 中， score 值在 min 和 max 之间
# (默认包括 score 值等于 min 或 max )的成员的数量
zcount k min max 

# 返回有序集 key 中，指定区间内的成员
# 其中成员的位置按 score 值递增(从小到大)来排序。
zrange k start end withscores
# 按 score 值递减(从大到小)
zrevrange k start end withscores

# 返回所有 score 值介于 min 和 max 之间
# (包括等于 min 或 max )的成员
zrangebyscore k min max withscores limit offset count

# 返回有序集 key 中成员 member 的排名
# 其中有序集成员按score 值递增(从小到大)顺序排列。
zrank k m
zrevrank k m
# 返回有序集 key 中，成员 member 的 score 值。
zscore k m
zrem k m1 m2
# 移除有序集 key 中，指定排名(rank)区间内的所有成员
zremrangebyrank k start end

zremrangebyscore k min max
```

## info

### `info memory`
**used_memory**, include used swap space

**used_memory_human**

**used_memory_rss**, same as `top` or `ps`, exclude used swap space

**mem_fragmentation_ratio** same as `used_memory_rss / used_memory` fragmentation ratio 

health value is 1.03 for jemalloc

**mem_allocator** libc, jemalloc or tcmalloc
