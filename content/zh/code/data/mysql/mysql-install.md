
---
title: "Install MySQL"
---

## install

### container

```shell
# $PWD/my.cnf
# -v $PWD:/etc/mysql/conf.d
# -v $PWD/datadir:/var/lib/mysql
docker run -d -it --name mysql57 \
 -e MYSQL_ROOT_PASSWORD=root \
 -p 3306:3306 \
 mysql:5.7

# withour configuration file
docker run --name mysql -d \
	-p 3306:3306 \
	-e MYSQL_ROOT_PASSWORD=root \
	mysql --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

### centos

```shell
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql-community-server
# start/stop
systemctl start mysqld
grep "password" /var/log/mysqld.log
yum -y remove mysql57-community-release-el7-10.noarch
```

### windows

```bat
mysqld --initialize --user=mysql --console
:: mysqld --remove MySQL
mysqld --install MySQL
net start MySQL
net stop MySQL
:: set password=password('root')
```

**my.conf**
```conf
[mysqld]
#设置3306端口
port = 3306
# 设置mysql的安装目录
basedir=C:\Application\mysql-5.7.27-winx64
# 设置mysql数据库的数据的存放目录
datadir=C:\Application\mysql-5.7.27-winx64\data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB

[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
```

## mysqladmin

```shell
# change password
mysqladmin -u myuser -pold_password password new_password

mysqladmin -uroot -p processlist
mysqladmin -uroot -p status
mysqladmin -uroot -p variables
```
