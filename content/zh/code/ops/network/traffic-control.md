
---
title: "Traffic Control"
---

## 流量控制器TC（Traffic Control）

### 弱网测试的指标

- 带宽（吞吐量）：单位时间内传输的数据量，单位通常是：每秒比特数：bps。带宽反映了网络的传输能力，越大越好。
- 丢包：数据丢包个数=发送的数据包数-接收的数据包数。丢包反映了网络可靠性，越小越好。
- 时延：数据包从发送开始到接收到该数据，所耗费的时间，单位通常是ms。时延反映了网络的速度，越小越好。
- 抖动：指时延的变化，即两个数据包时延的差值。抖动反映了网络的稳定性，越小越好。
- 乱序：指接收到的数据包顺序和发送顺序不一致的次数。乱序反映了网络的稳定性，越小越好。当乱序比较严重的时候，丢包也会比较严重，所以一般都以丢包指标为主，忽略乱序指标。

流量控制，主要是通过在输出端口处建立一个队列来实现流量控制

```shell
# 查看eth0网卡的规则信息
tc -s qdisc ls dev eth0
tc qdisc show
# 删除规则
tc qdisc del dev eth0 root

# 设置丢包率为10%
tc qdisc add dev eth0 root netem loss 10%
# 丢包率为范围（10%- 30%）
tc qdisc add dev eth0 root netem loss 10% 30%

# 廷时100ms
tc qdisc add dev eth0 root netem delay 100ms
# 廷时100ms ± 10ms
tc qdisc add dev eth0 root netem delay 100ms 10ms
# 廷时100ms 25%概率以±10ms波动延迟
tc qdisc add dev eth0 root netem delay 100ms 10ms 25%

# 最大带宽1Mbit
tc qdisc add dev enp0s8 root handle 1:htb default 1
tc class add dev enp0s8 parent 1:0 classid 1:1 htb rate 1Mbit
```
