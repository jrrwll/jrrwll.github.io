
---
title: "ClickHouse Client"
---

## client

```shell
# -u default
clickhouse client --password mypassword
# -m --multiline 允许sql包含换行
# -n --multiquery 允许执行多个语句
clickhouse-client --multiquery --multiline < /tmp/test.sql
```

## sql
```sql
show databases;
create database test;
use test;

show tables;
select name from system.tables where database = 'test';
```
