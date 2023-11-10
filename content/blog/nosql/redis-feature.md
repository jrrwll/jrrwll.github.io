---
title: "Redis 特性"
linkTitle: "Redis 特性"
date: 2022-05-21
weight: 12
---

## 发布订阅

### 事件推送
> https://redis.io/docs/manual/keyspace-notifications/

事件类型
- #### `__keyspace@db:key_pattern` 键空间通知（key-space notification）

- #### `__keyevent@db:ops_type` 键事件通知（key-event notification）

执行`del mykey`之后，相当于发送以下事件
```redis
publish __keyspace@0__:mykey del
publish __keyevent@0__:del mykey
```

开启E键事件通知，针对x过期事件(键访问触发检查而删除该键，或者后台查找并删除)
`config set notify-keyspace-events Ex`

> Redis的过期事件发生的时机，是真正删除的时候，而不是在理论上生存时间达到零值时生成

