
---
title: "MySQL Cluster"
---

## install

### container

```shell
docker run -d -p 3306:3306 --hostname mysql-master --name mymaster \
	-e MYSQL_ROOT_PASSWORD=root \
	-v ~/share/data/master:/var/lib/mysql \
	mysql:5.7 --explicit_defaults_for_timestamp \
	--server-id=1 \
	--log-bin=master-bin --log-bin-index=master-bin.index
	
docker run -d -p 3316:3306 --hostname mysql-slave1 --name myslave1 \
	-e MYSQL_ROOT_PASSWORD=root \
	-v ~/share/data/slave1:/var/lib/mysql \
	mysql:5.7 --explicit_defaults_for_timestamp \
	--server-id=2  \
	--relay-log-index=slave-relay-bin.index --relay-log=slave-relay-bin 

docker exec mymaster sh -c '
exec mysqldump --all-databases -uroot -p"$MYSQL_ROOT_PASSWORD"
' > all-databases.sql
```

## get start

```sql
create user repl identified by 'repl';
grant replication slave, file on *.* to 'repl'@'192.168.%';
```

**master my.cnf**

```ini
[mysqld]
server-id=1

log-bin=master-bin
log-bin-index=master-bin.index

binlog-do-db=test
binlog-ignore-db=mysql
```

**slave my.cnf**

```ini
[mysqld]
server-id=2
relay-log-index=slave-relay-bin.index
relay-log=slave-relay-bin 

replicate-do-db=test
replicate-ignore-db=mysql
```

### create my.cnf for master and slaves

```sh
cat <<EOF > mysql-master.cnf
[mysqld]
server-id=1
log-bin=master-bin
log-bin-index=master-bin.index
EOF

cat <<EOF > mysql-slave1.cnf
[mysqld]
server-id=2
relay-log-index=slave-relay-bin.index
relay-log=slave-relay-bin 
EOF

cat <<EOF > mysql-slave2.cnf
[mysqld]
server-id=3
relay-log-index=slave-relay-bin.index
relay-log=slave-relay-bin 
EOF
```

### start slave

```sql
-- in slave
stop slave io_thread for channel '';
change master to master_host='192.168.254.153', master_port=3306, master_user='repl', master_password='repl';
start slave;
show slave status\G
show global status like '%slave%';

-- in master
show master status;
-- binlog
show variables like "log%";
```

```sh
create database web;
create user web identified by 'web';
grant all on web.* to 'web'@'%';
```
