---
title: "PrestoDB"
---

## install

### client
> https://prestodb.io/docs/current/installation/cli.html

```shell
./presto --server localhost:8080 --catalog hive --schema default
```

### container
> https://hub.docker.com/r/ahanaio/prestodb-sandbox

```shell
docker run -d --name presto \
-p 8080:8080 \
ahanaio/prestodb-sandbox

docker exec -it presto presto-cli
docker exec -it presto presto-cli --catalog tpcds --schema sf10
```

```sql
show catalogs;
show schemas in system;
-- show schemas in tpcds;
use tpcds.sf10;
```

## connector
> https://prestodb.io/docs/current/connector.html


### memory

**etc/catalog/memory.properties**

```properties
connector.name=memory
memory.max-data-per-node=128MB
```

```shell
create table memory.default.nation as
select * from tpch.tiny.nation;

insert into memory.default.nation
select * from tpch.tiny.nation;

select * from memory.default.nation;
drop table memory.default.nation;
```

### localfile

**etc/catalog/localfile.properties**

```properties
connector.name=localfile
presto-logs.http-request-log.location=/path/to/dir_or_file
presto-logs.http-request-log.pattern=
```

```sql
show tables from localfile.logs;
```

### postgresql

**etc/catalog/postgresql.properties**

```properties
connector.name=postgresql
connection-url=jdbc:postgresql://example.net:5432/database
connection-user=root
connection-password=root
```

```sql
show schemas from postgresql;
show tables from postgresql.my_schema;

describe postgresql.my_schema.my_table;
show columns from postgresql.my_schema.my_table;
select * from postgresql.my_schema.my_table;
```

### mysql

```properties
connector.name=mysql
connection-url=jdbc:mysql://example.net:3306?serverTimezone=UTC
connection-user=root
connection-password=secret
```

### hive
> https://prestodb.io/docs/current/connector/hive.html

**etc/catalog/hive.properties**

```properties
connector.name=hive-hadoop2
hive.metastore.uri=thrift://example.net:9083
hive.config.resources=/etc/hadoop/conf/core-site.xml,/etc/hadoop/conf/hdfs-site.xml
```

Presto JVM Config: `-DHADOOP_USER_NAME=hdfs_user`

### elasticsearch

```properties
connector.name=elasticsearch
elasticsearch.host=localhost
elasticsearch.port=9200
elasticsearch.default-schema-name=default
```

### kafka

```properties
connector.name=kafka
kafka.table-names=table1,table2
kafka.nodes=host1:port,host2:port
```
