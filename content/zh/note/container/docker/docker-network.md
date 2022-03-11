
---
title: "Docker Network"
---

## `--link`

```shell
# source container
docker run --name mysql -e MYSQL_ROOT_PASSWORD=server -d mysql

# received container
# 在nginx的容器中，使用db或者aliasmysql作为连接地址来连接MySQL服务
docker run --name nginx --link mysql:aliasmysql -d nginx
```

docker将source container中定义的环境变量全部导入到received container中，在received container中可以通过环境变量来获取连接信息

通过给/etc/hosts中加入名称和IP的解析关系来实现

## network

docker容器的网络有五种模式：

1）**bridge模式**，--net=bridge(默认)
这是dokcer网络的默认设置，为容器创建独立的网络命名空间，容器具有独立的网卡等所有单独的网络栈，是最常用的使用方式。
在docker run启动容器的时候，如果不加--net参数，就默认采用这种网络模式。安装完docker，系统会自动添加一个供docker使用的网桥docker0，我们创建一个新的容器时，
容器通过DHCP获取一个与docker0同网段的IP地址，并默认连接到docker0网桥，以此实现容器与宿主机的网络互通。

 2）**host模式**，--net=host
这个模式下创建出来的容器，直接使用容器宿主机的网络命名空间。
将不拥有自己独立的Network Namespace，即没有独立的网络环境。它使用宿主机的ip和端口。

3）**none模式**，--net=none
为容器创建独立网络命名空间，但不为它做任何网络配置，容器中只有lo，用户可以在此基础上，对容器网络做任意定制。
这个模式下，dokcer不为容器进行任何网络配置。需要我们自己为容器添加网卡，配置IP。
因此，若想使用pipework配置docker容器的ip地址，必须要在none模式下才可以。

 4）**container模式**，--net=container:NAME_or_ID
与host模式类似，只是容器将与指定的容器共享网络命名空间。
这个模式就是指定一个已有的容器，共享该容器的IP和端口。除了网络方面两个容器共享，其他的如文件系统，进程等还是隔离开的。容器可以相以localhost访问，构成一个统一的整体。

 5）**用户自定义**：docker 1.9版本以后新增的特性，允许容器使用第三方的网络实现或者创建单独的bridge网络，提供网络隔离能力	

在用户定义网络模式下，开发者可以使用任何docker支持的第三方网络driver来定制容器的网络。并且，docker 1.9以上的版本默认自带了bridge和overlay两种类型的自定义网络driver。可以用于集成calico、weave、openvswitch等第三方厂商的网络实现。

除了docker自带的bridge driver，其他的几种driver都可以实现容器的跨主机通信。而基于bdrige driver的网络，docker会自动为其创建iptables规则，保证与其他网络之间、与docker0之间的网络隔离。

### WARNING: IPv4 forwarding is disabled. Networking will not work.

```shell
echo "net.ipv4.ip_forward=1" >>/usr/lib/sysctl.d/00-system.conf
systemctl restart network && systemctl restart docker
```


## volume

- ### backup

```shell
docker volume create data
docker run -v data:/path/to/data --name data_container \
	-d -it --rm \
	busybox /bin/sh

docker run --rm --volumes-from data_container -v $(pwd):/backup busybox tar cvf /backup/data_backup.tar path/to/data
```

- ### restore

```shell
docker run -v data:/path/to/data --name data_container2 \
	-d -it --rm \
	busybox /bin/bash

docker run --rm --volumes-from data_container2 \
	-v $(pwd):/backup busybox \
	bash -c "cd /path/to/data && tar xvf /backup/data_backup.tar --strip 1"
```
