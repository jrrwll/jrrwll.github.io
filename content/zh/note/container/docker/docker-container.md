
---
title: "Docker Container"
---

## run

```shell
# 创建一个守护态的Docker容器
docker run -itd ubuntu:14.04 /bin/bash 

# docker ps
# 进入容器
docker attach <container_id> 
# 当多个窗口同时使用该命令进入该容器时，所有的窗口都会同步显示

# enter a Docker container. 
docker exec -it CONTAINER_NAME /bin/bash

```

```shell
# 创建一个新的容器并运行一个命令
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

OPTIONS说明：

- **-a stdin:** 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
- **-d:** 后台运行容器，并返回容器ID；
- **-i:** 以交互模式运行容器，通常与 -t 同时使用；
- **-p:** 端口映射，格式为：宿主端口:容器端口
- **-v**: 主机的目录映射到容器
- **-t:** 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
- **--name="cname":** 为容器指定一个名称；
- **--dns 8.8.8.8:** 指定容器使用的DNS服务器，默认和宿主一致；
- **--dns-search example.com:** 指定容器DNS搜索域名，默认和宿主一致；
- **-h "hostname":** 指定容器的hostname；
- **-e property=value:** 设置环境变量；
- **--env-file=[]:** 从指定文件读入环境变量；
- **--cpuset="0-2" or --cpuset="0,1,2":** 绑定容器到指定CPU运行；
- **-m :**设置容器使用内存最大值；
- **--net="bridge":** 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；
- **--link=[]:** 添加链接到另一个容器；
- **--expose=[]:** 开放一个端口或一组端口；

```shell
docker start # 启动一个或多少已经被停止的容器
docker stop # 停止一个运行中的容器
docker restart # 重启容器

docker pause # 暂停容器中所有的进程。
docker unpause # 恢复容器中所有的进程。
```

## attach

```shell
# 确保CTRL-D或C	TRL-C不会关闭容器
docker attach --sig-proxy=false
```

## rm

OPTIONS说明：

- **-f :**通过SIGKILL信号强制删除一个运行中的容器
- **-l :**移除容器间的网络连接，而非容器本身
- **-v :**-v 删除与容器关联的卷

## create

```shell
# 创建一个新的容器但不启动它
docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
```

## exec

```shell
# 在运行的容器中执行命令
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

# tty size
docker exec -it -e LINES=$(tput lines) -e COLUMNS=$(tput cols) <cid> bash

```

OPTIONS说明：

- **-d :**分离模式: 在后台运行
- **-i :**即使没有附加也保持STDIN 打开
- **-t :**分配一个伪终端

## kill

```shell
# Kill one or more running containers
# <signal>
# KILL
docker kill -s <signal>
```

## cp

```shell
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
```
