
---
title: "Redis Sentinel"
---

## sentinel

```shell
ping

info replication

info server

info sentinel

sentinel auth-pass <name> <password>

sentinel masters

sentinel master <name>
sentinel slaves <name>

# 返回指定master的ip和端口
# 正在进行failover或者failover已经完成，将显示被提升为master的slave的ip和端口
sentinel get-master-addr-by-name <name> 

# 重置名字匹配该正则表达式的所有的master的状态信息
sentinel reset <pattern> 

# 执行failover，无需其他sentinel同意
sentinel failover <master name> 

# 监听一个新的master
sentinel monitor <name> <ip> <port> <quorum>
# 放弃对某个master的监听
sentinel remove <name>

# 改变指定master的配置，支持多个<option> <value>
sentinel set <name> <option> <value>

```

### conf
```shell
# 26379
redis-sentinel /path/to/sentinel.conf

redis-server /path/to/sentinel.conf --sentinel
```

### sentinel.conf
sentinel monitor mymaster 127.0.0.1 6379 2
sentinel down-after-milliseconds mymaster 60000
sentinel failover-timeout mymaster 180000
sentinel parallel-syncs mymaster 1

sentinel monitor resque 192.168.1.3 6380 4
sentinel down-after-milliseconds resque 10000
sentinel failover-timeout resque 180000
sentinel parallel-syncs resque 5


### sentinel monitor mymaster 127.0.0.1 6379 2
2 当集群中有2个sentinel认为master死了时，才能真正认为该master已经不可用了

### sentinel <option_name> <master_name> <option_value>
所有的配置都可以在运行时用命令SENTINEL SET command动态修改。

#### down-after-milliseconds
sentinel会向master发送心跳PING来确认master是否存活，如果master在“一定时间范围”内不回应PONG 或者是回复了一个错误消息，那么这个sentinel会主观地(单方面地)认为这个master已经不可用了

#### parallel-syncs
在发生failover主备切换时，这个选项指定了最多可以有多少个slave同时对新的master进行同步，这个数字越小，完成failover所需的时间就越长，但是如果这个数字越大，就意味着越多的slave因为replication而不可用。可以通过将这个值设为 1 来保证每次只有一个slave处于不能处理命令请求的状态。

####
sentinel对于不可用有两种不同的看法，一个叫主观不可用(SDOWN),另外一个叫客观不可用(ODOWN)。SDOWN是sentinel自己主观上检测到的关于master的状态，ODOWN需要一定数量的sentinel达成一致意见才能认为一个master客观上已经宕掉，各个sentinel之间通过命令SENTINEL is_master_down_by_addr来获得其它sentinel对master的检测结果。


min-slaves-to-write 1
min-slaves-max-lag 10

当一个redis是master时，如果它不能向至少一个slave写数据(上面的min-slaves-to-write指定了slave的数量)，它将会拒绝接受客户端的写请求。由于复制是异步的，master无法向slave写数据意味着slave要么断开连接了，要么不在指定时间内向master发送同步数据的请求了(上面的min-slaves-max-lag指定了这个时间)


### 向sentinel订阅消息
```shell
psubscribe *
psubscribe sdown

```

```
<instance-type> <name> <ip> <port> @ <master-name> <master-ip> <master-port>

所有收到的消息的消息格式

    +reset-master <instance details> -- 当master被重置时.
    +slave <instance details> -- 当检测到一个slave并添加进slave列表时.
    +failover-state-reconf-slaves <instance details> -- Failover状态变为reconf-slaves状态时
    +failover-detected <instance details> -- 当failover发生时
    +slave-reconf-sent <instance details> -- sentinel发送SLAVEOF命令把它重新配置时
    +slave-reconf-inprog <instance details> -- slave被重新配置为另外一个master的slave，但数据复制还未发生时。
    +slave-reconf-done <instance details> -- slave被重新配置为另外一个master的slave并且数据复制已经与master同步时。
    -dup-sentinel <instance details> -- 删除指定master上的冗余sentinel时 (当一个sentinel重新启动时，可能会发生这个事件).
    +sentinel <instance details> -- 当master增加了一个sentinel时。
    +sdown <instance details> -- 进入SDOWN状态时;
    -sdown <instance details> -- 离开SDOWN状态时。
    +odown <instance details> -- 进入ODOWN状态时。
    -odown <instance details> -- 离开ODOWN状态时。
    +new-epoch <instance details> -- 当前配置版本被更新时。
    +try-failover <instance details> -- 达到failover条件，正等待其他sentinel的选举。
    +elected-leader <instance details> -- 被选举为去执行failover的时候。
    +failover-state-select-slave <instance details> -- 开始要选择一个slave当选新master时。
    no-good-slave <instance details> -- 没有合适的slave来担当新master
    selected-slave <instance details> -- 找到了一个适合的slave来担当新master
    failover-state-send-slaveof-noone <instance details> -- 当把选择为新master的slave的身份进行切换的时候。
    failover-end-for-timeout <instance details> -- failover由于超时而失败时。
    failover-end <instance details> -- failover成功完成时。
    switch-master <master name> <oldip> <oldport> <newip> <newport> -- 当master的地址发生变化时。通常这是客户端最感兴趣的消息了。
    +tilt -- 进入Tilt模式。
    -tilt -- 退出Tilt模式。
```