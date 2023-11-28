---
title: "Redis 应用场景"
linkTitle: "Redis 应用场景"
date: 2023-11-08
---

## kv缓存

```shell
# 打开缓存模式
config set maxmemory 20gb
```

当数据量不断增长时，可使用 Codis 或者 Redis-Cluster 集群来扩容。

淘汰范围和策略选定 `allkeys-lru`。allkeys 表示对 Redis 内部所有的 key 都有可能被淘汰，而volatile只淘汰带过期时间的。

## 分布式锁

```lua
-- 加锁
set $lock_key $lock_value nx ex=5

-- 释放锁
if redis.call("get", KEYS[1]) == ARGV[1] then
    return redis.call("del", KEYS[1])
else
    return 0
end
-- 或者等价于
del_if_equals $lock_key $lock_value
```

官方推荐的分布式锁叫 RedLock，需要 3 台以上独立的 Redis 实例。不过一般性能敏感的业务，还是使用上面最简单的分布式锁。这是因为，Redis 集群发生主从切换的概率也并不高，即使发生了主从切换出现锁丢失的概率也很低，因为主从切换往往都有一个过程，这个过程的时间通常会超过锁的过期时间，也就不会发生锁的异常丢失。

## 延时队列

有很多专业的消息中间件支持延时消息功能，比如 RabbitMQ 和 NSQ。Redis 也可以，我们可以使用 zset 来实现这个延时队列。zset 里面存储的是 value／score 键值对，我们将 value 存储为序列化的任务消息，score 存储为下一次任务消息运行的时间（deadline），然后轮询 zset 中 score 值大于 now 的任务消息进行处理。

```python
# 生产延时消息
zadd(queue_key, now_ts + 5, task_json)

# 消费延时消息
while True:
  task_json = zrevrangebyscore(queue_key, now_ts, 0, 0, 1)
  if task_json:
    ok = zrem(queue_key, task_json)
    if ok:
      process_task(task_json)
  else:
    sleep(1000) # sleep 1s
```

将轮询和争抢操作原子化，这样就可以避免竞争浪费

```lua
local res = nil
local tasks = redis.pcall("zrevrangebyscore", KEYS[1], ARGV[1], 0, "LIMIT", 0, 1)
if tasks > 0 then
  local ok = redis.pcall("zrem", KEYS[1], tasks[1])
  if ok > 0 then
    res = tasks[1] 
  end
end
return res
```

## 定时任务

分布式定时任务实现方式：

- **master-workers**

master 负责管理时间，到点了就将任务消息扔到消息中间件里，然后worker们负责监听这些消息队列来消费消息。著名的 Python 定时任务框架 `Celery` 就是这么干的。但是 `Celery` 有一个问题，那就是 master 是单点的，如果这个 master 挂了，整个定时任务系统就停止工作了。

- **multi-master**

类似于 Java 里面的 Quartz 框架，采用数据库锁来控制任务并发。会有多个进程，每个进程都会管理时间，时间到了就使用数据库锁来争抢任务执行权，抢到的进程就获得了任务执行的机会，然后就开始执行任务，这样就解决了 master 的单点问题。

这种模型有一个缺点，那就是会造成竞争浪费问题，不过通常大多数业务系统的定时任务并没有那么多，所以这种竞争浪费并不严重。还有一个问题它依赖于分布式机器时间的一致性，如果多个机器上时间不一致就会造成任务被多次执行，这可以通过增加数据库锁的时间来缓解。

Redis实现方式：

```python
# 注册定时任务
hset tasks_key task_name trigger_rule
# 获取定时任务列表
hgetall tasks_key
# 争抢任务
set task_name_key true nx ex=5

# 任务列表变更(滚动升级)
# 轮询版本号，有变化就重加载任务列表，重新调度时间有变化的任务
set tasks_version new_version
get tasks_version
```

## 频率控制

限制单个用户内容生产速度，不同等级的用户会有不同的频率控制参数。将用户的行为理解为一个时间序列，保证在一定的时间内限制单个用户的时间序列的长度，超过了这个长度就禁止用户的行为。它可以用 Redis 的 zset 来实现。

```python
# 控制用户的 ugc 行为为每小时最多 N 次
hist_key = "ugc:${user_id}"
with redis.pipeline() as pipe:
  # 记录当前的行为
  pipe.zadd(hist_key, ts, uuid)
  # 保留1小时内的行为序列
  pipe.zremrangebyscore(hist_key, 0, now_ts - 3600)
  # 获取这1小时内的行为数量
  pipe.zcard(hist_key)
  # 设置过期时间，节约内存
  pipe.expire(hist_key, 3600)
  # 批量执行
  _, _, count, _ = pipe.exec()
  return count > N
```

## 服务发现

通常会选用 zookeeper、etcd、consul 等分布式配置数据库来作为服务列表的存储。它们有非常及时的通知机制来通知服务消费者服务列表发生了变更。使用Redis的zset也可以实现服务发现。使用 zset 来保存单个服务列表。多个服务列表就使用多个 zset 来存储。

```shell
# zset 的 value 和 score 分别存储服务的地址和心跳的时间。服务提供者需要使用心跳来汇报自己的存活，每隔几秒调用一次 zadd。服务提供者停止服务时，使用 zrem 来移除自己。
zadd service_key heartbeat_ts addr
zrem service_key addr
```

```shell
# 使用一个额外的线程来清理服务列表中的过期项
zremrangebyscore service_key 0 now_ts - 30  # 30s 都没来心跳
```

```shell
# 当服务列表变更时，递增版本号，消费者通过轮询版本号的变化来重加载服务列表。
# 执行 (zadd, zrem, zremrangebyscore)
incr service_version_key
# 当任意的服务列表版本号发生变更时，递增全局版本号。消费者先轮询全局版本号，有变化，则比对依赖的服务列表的子版本号，然后加载有变更的服务列表。
incr global_service_version_key
```

## 位图

使用Redis hash的value存储位图字符串，可用于签到系统。比如签到一共三个状态：签到有三种状态，未签到、已签到和补签，并且每天签到一次。

```shell
# 一个签到状态需要两个位来记录，一个月的存储空间则需要 8 个字节
hset sign_user_key yyyy_mm bits
```

### 咆哮位图

https://github.com/aviggiano/redis-roaring

对大位图进行了分段存储，全位零的段可以不用存。然后对每个段设计了稀疏存储结构，如果这个段上置 1 的位不多，可以只存储它们的偏移量整数。这样位图的存储空间就得到了非常显著的压缩。

## 模糊计数

实现一个实时的活跃计数，可以在 Redis 里面维护一个 set 集合，来一个用户，就 sadd 一下，最终集合的大小就是我们需要的 UV 数字，但是空间浪费严重。这时可以使用 Redis 提供的 `HyperLogLog` 模糊计数功能，它是一种概率计数，有一定的误差，误差大约是 0.81%。但是空间占用很小，其底层是一个位图，它最多只会占用 12k 的存储空间。而且在计数值比较小的时候，位图使用稀疏存储，空间占用就更小了。

```shell
# 记录用户
pfadd sign_uv_day user_id
# 获取记录数量
pfcount sign_uv_day
```

需要UV的数字准确性，则可以使用咆哮位图。

## 布隆过滤器

```python
def get_user_state(user_id):
  exists = bloomfilter.is_user_exists(user_id)
  # 返回false，则说明一定不存在
  if not exists:
    return {}
  return get_user_state0(user_id)

def save_user_state(user_id, state):
  bloomfilter.set_user_exists(user_id)
  save_user_state0(user_id, state)
```
