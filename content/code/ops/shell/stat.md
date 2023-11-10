
---
title: "Stat"
---

## os

```shell
# os name
cat /etc/issue
cat /etc/os-release
cat /usr/lib/os-release
uname -a
```

## mem

```shell
# 查看内存占用
ps aux --sort -rss | head
top -b -o +%MEM | head -n 20 | tail -20
top -c -b -o +%MEM | head -n 20 | tail -20
```

## net

```shell
# 统计22端口连接数
netstat -nat | grep -i 22 | wc -l
# 已打开的连接
netstat -na | grep ESTABLISHED | wc -l
# 查找连接最多的IP地址
netstat -na | grep ESTABLISHED | awk {print $5} | awk -F: {print $1} | sort | uniq -c | sort -r +0n
netstat -na | grep SYN | awk {print $5} | awk -F: {print $1} | sort | uniq -c | sort -r +0n
```
