
---
title: "Mac Stat"
---

## info

```shell
# 查看内核参数
ulimit -a
```

## mem

```shell
# 参看内存
top -l 1 | head -n 10 | grep PhysMem
top -l 1 | awk '/PhysMem/'
echo -e "\n$(top -l 1 | awk '/PhysMem/';)\n"

system_profiler | grep Processor
```

## network

```shell
# 查看端口
lsof -i tcp:8080
# -i参数表示网络链接，:80指明端口号，该命令会同时列出PID，方便kill
lsof -i -P | grep -i "listen"

netstat -an | grep java
```
