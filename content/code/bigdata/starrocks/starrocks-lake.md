
---
title: "StarRocks Lake"
---

> https://docs.starrocks.io/zh/docs/data_source/data_lakes/

## Catalog（数据目录）

> 实现在一套系统内同时维护内、外部数据，方便您轻松访问并查询存储在各类外部源的数据。

- **Internal catalog**: 每个 StarRocks 集群都有且只有一个 internal catalog 名为 `default_catalog`。
- **External catalog**: 外部数据目录，用于连接外部 metastore。可以通过 external catalog 直接查询外部数据，无需进行数据导入或迁移。

```sql
show databases from default_catalog;

set catalog hive_catalog;
use hive_catalog.hive_db;
show create catalog hive_catalog;
drop catalog hive_catalog;

-- 跨 catalog 查询数据
select * from hive_catalog.hive_db.hive_table h 
join default_catalog.olap_db.olap_table o
on h.id = o.id
where h.type = 1;
```

### JDBC Catalog

```sql
create external catalog mysql_jdbc_catalog
comment 'MySQL JDBC Catalog'
properties (
    "type" = "jdbc",
    "user" = "mysql",
    "password" = "mysql",
    "jdbc_uri"="jdbc:mysql://127.0.0.1:3306/jdbc_test",
    "driver_url"="https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.28/mysql-connector-java-8.0.28.jar",
    "driver_class"="com.mysql.cj.jdbc.Driver"
);

create external catalog postgres_jdbc_catalog
comment 'PostgreSQL JDBC Catalog'
properties (
    "type" = "jdbc",
    "user" = "postgres",
    "password" = "postgres",
    "jdbc_uri"="jdbc:postgresql://127.0.0.1:5432/jdbc_test",
    "driver_url"="https://repo1.maven.org/maven2/org/postgresql/postgresql/42.3.3/postgresql-42.3.3.jar",
    "driver_class"="org.postgresql.Driver"
);
```

### Elasticsearch Catalog

```sql
create external catalog es_catalog
comment 'Elasticsearch Catalog'
properties (
    "type" = "es",
    -- -- es8+ 不需要此参数
    -- "es.type" = "_doc",
    -- hosts 参数值必须和 GET /_nodes/http 返回的地址一致
    "hosts"="https://127.0.0.1:9200",
    "es.net.ssl" = "true",
    "user" = "admin",
    "password" = "admin",
    -- 是否仅使用 hosts 指定的地址。如果 StarRocks 可以访问 Elasticsearch 集群内部数据节点的地址，则建议保持默认值 false。
    "es.nodes.wan.only" = "true"
);
```

#### esquery()

可以将一些无法用 SQL 表述的 Elasticsearch 查询（如 Match 查询和 Geoshape 查询等）下推给 Elasticsearch 进行过滤处理。

```sql
select * from es_table where esquery(col_name, '{
   "match": {
      "col_name": "StarRocks on elasticsearch"
   }
}');
```

## External Table（外部表）

> 外部表指的是保存在其他数据源中的数据表，而 StartRocks 只保存表对应的元数据。目前支持写StartRocks外部表，读其他类型外部表(已被Catalog替代，不推荐使用)

### StartRocks外部表

通过 INSERT INTO 写入数据至 StarRocks 外表，可以将源集群的数据写入至目标集群。借助这一能力，可以实现如下目标：
- 集群间的数据同步
- 读写分离。向源集群中写入数据，并且源集群的数据变更同步至目标集群，目标集群提供查询服务

```sql
create external table external_sr (
    c1 bigint,
    c2 varchar(1000)
)
engine = olap
distributed by hash(c1)
properties (
    "host" = "127.0.0.1",
    -- fe rpc_port, 其中 be brpc_port=8060 也需要开放
    "port" = "9020",
    "user" = "user",
    "password" = "passwd",
    "database" = "db_test",
    "table" = "table_test"
);

insert into external_sr values (1, 'hello');
insert into external_t select * from other_table;
```

## File External Table (文件外部表)

```sql
create external table external_file (
    c1 bigint,
    c2 varchar(1000)
)
engine = file
properties (
    -- path 以 / 结尾，则扫描路径下所有文件，否则视为单一文件
    -- hdfs://hdfs_ip:8020/path
    -- s3://bucket/path
    "path" = "<file_path>",
    -- parquet、orc、avro、rctext 或 rcbinary、sequence
    "format" = "<file_format>"
    -- 是否递归查询路径下的所有文件。默认值：true
    "enable_recursive_listing" = "true"
    -- 是否支持在 path 内使用通配符（*）。默认值：false
    "enable_wildcards" = "false"
    -- 认证参数
    "aws.s3.enable_ssl" = "false",
    "aws.s3.enable_path_style_access" = "true", -- minio需要设置为true
    "aws.s3.endpoint" = "<s3_endpoint>",
    "aws.s3.access_key" = "<iam_user_access_key>",
    "aws.s3.secret_key" = "<iam_user_secret_key>"
)
```

### files()

```sql
-- desc files(...)

select * from files(
     "path" = "s3://inserttest/parquet/par-dup.parquet",
     "format" = "parquet",
     "aws.s3.access_key" = "ak",
     "aws.s3.secret_key" = "sk",
     "aws.s3.region" = "us-west-2"
);
```

```sql
insert into files(
    "path" = "hdfs://hdfs_ip:8020/unload/partitioned/",
    "format" = "parquet",
    "hadoop.security.authentication" = "simple",
    "username" = "x",
    "password" = "x",
    "compression" = "lz4",
    "partition_by" = "sales_time"
);
select * from sales_records;
```
