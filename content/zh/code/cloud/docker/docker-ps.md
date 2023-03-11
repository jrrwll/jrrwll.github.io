
---
title: "Docker Ps"
---

## ps

```shell
docker ps -a
# Show the latest created container, Only display numeric IDs
docker ps -lq

docker ps --format 'table {{.Names}}\t{{.Image}}'

vim ~/.docker/config.json.
{
  "psFormat": "table {{.Names}}\\t{{.Image}}\\t{{.RunningFor}} ago\\t{{.Status}}\\t{{.Command}}",
  "imagesFormat": "table {{.Repository}}\\t{{.Tag}}\\t{{.ID}}\\t{{.Size}}"
}
```

OPTIONS说明：

- **-a :**显示所有的容器，包括未运行的。
- **-f :**根据条件过滤显示的内容。
- **--format :**指定返回值的模板文件。
- **-l :**显示最近创建的容器。
- **-n :**列出最近创建的n个容器。
- **--no-trunc :**不截断输出。
- **-q :**静默模式，只显示容器编号。
- **-s :**显示总的文件大小。

## inspect

```shell
# 显示容器的第一个进程的PID
docker inspect -f {{.State.Pid}} <container_id> 

docker inspect --format='{{.RootFS.Layers}}' <image_id> 

docker inspect --format='{{.NetworkSettings.IPAddress }}' <container_id> 
```

获取容器/镜像的元数据

OPTIONS说明：

- **-f :**指定返回值的模板文件。
- **-s :**显示总的文件大小。
- **--type :**为指定类型返回JSON。

## diff

```shell
docker diff <container_id>
```

## top

查看容器中运行的进程信息

```shell
docker top [OPTIONS] CONTAINER [ps OPTIONS]

# 查看所有运行容器的进程信息
for i in  `docker ps |grep Up|awk '{print $1}'`;do echo \ &&docker top $i; done
```

## logs 

获取容器的日志

```shell
docker logs [OPTIONS] CONTAINER
tail -f /var/lib/docker/containers/<cid>/<cid>-json.log

# 用 fluentd 收集容器日志
docker run -d \
	--log-driver=fluentd \
	--log-opt fluentd-address=${fluentd_address} \
	--log-opt tag="docker.{{.Name}}" \
nginx
```

OPTIONS说明：

- **-f :** 跟踪日志输出
- **--since :**显示某个开始时间的所有日志
- **-t :** 显示时间戳
- **--tail :**仅列出最新N条容器日志

### Docker 引擎日志

Ubuntu(14.04)           /var/log/upstart/docker.log
Ubuntu(16.04)           journalctl -u docker.service
CentOS 7/RHEL 7/Fedora  journalctl -u docker.service
CoreOS                  journalctl -u docker.service
OpenSuSE                journalctl -u docker.service
OSX                     ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/log/docker.log
Debian GNU/Linux 7      /var/log/daemon.log
Debian GNU/Linux 8  journalctl -u docker.service
Boot2Docker /var/log/docker.log

## events

从服务器获取实时事件

```shell
docker events [OPTIONS]
```

OPTIONS说明：

- **-f ：**根据条件过滤事件；
- **--since ：**从指定的时间戳后显示所有事件; --since="2016-07-01"
- **--until ：**流水时间显示到指定的时间为止；

## export

将文件系统作为一个tar归档文件导出到STDOUT。

```shell
docker export [OPTIONS] CONTAINER
```

OPTIONS说明：

- **-o :**将输入内容写到文件。

## wait

阻塞运行直到容器停止，然后打印出它的退出代码。

```shell
docker wait CONTAINER [CONTAINER...]
```

## port

列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口

```shell
docker port CONTAINER [PRIVATE_PORT[/PROTO]]
```

## $

```shell
# Delete all containers
docker rm $(docker ps -a -q)
# Delete all images
docker rmi $(docker images -q)

```

## `/var/lib/docker`

1. /var/lib/docker/devicemapper/devicemapper/data       #用来存储相关的存储池数据      
2. /var/lib/docker/devicemapper/devicemapper/metadata   #用来存储相关的元数据。
3. /var/lib/docker/devicemapper/metadata/               #用来存储 device_id. 大小. 以及传输_id. 初始化信息
4. /var/lib/docker/devicemapper/mnt                     #用来存储挂载信息 
5. /var/lib/docker/container/                           #用来存储容器信息
6. /var/lib/docker/graph/                               #用来存储镜像中间件及本身详细信息和大小 . 以及依赖信息
7. /var/lib/docker/repositores-devicemapper             #用来存储镜像基本信息
8. /var/lib/docker/tmp                                  #docker临时目录   
9. /var/lib/docker/trust                                #docker信任目录
10. /var/lib/docker/volumes                             #docker卷目录
