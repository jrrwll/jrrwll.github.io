---
title: "Doris"
---

## install

```shell
docker run -it -d --name doris \
    -p 9030:9030 -p 8030:8030 -p 8040:8040 \
    --privileged=true \
    taozex/doris:1.1.1

mysql -uroot -h127.0.0.1 -P 9030
```

## sql

### auth

```sql
show frontends\G
show proc '/backends'\G

-- alter system add backend "host:port";
set password for 'root' = password('root');
create user 'myuser' identified by 'Mypassword_123';

grant select_priv,load_priv,alter_priv,create_priv,drop_priv on resource * to 'myuser'@'%';
```

### table

```sql
create table `doris_test` (
 `c0` int(11) null comment "",
 `c1` date null comment "",
 `c2` datetime null comment "",
 `c3` varchar(65533) null comment ""
) engine=olap
  duplicate key(`c0`)
  distributed by hash(`c0`) buckets 1
  properties (
    "replication_num" = "1",
    "in_memory" = "false",
    "storage_format" = "DEFAULT"
);