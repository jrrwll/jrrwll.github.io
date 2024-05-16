---
title: "StarRocks SQL"
---

## sql

https://docs.starrocks.io/zh/docs/sql-reference/sql-functions/function-list/

```sql
-- https://docs.starrocks.io/zh/docs/cover_pages/functions_utility/
-- 2.1.2 0782ad7, StarRocks version 2.1.2
select current_version(), @@version_comment;

show proc '/frontends'\G
show proc '/backends'\G
```

### load

https://docs.starrocks.io/docs/loading/hdfs_load/#use-broker-load

```sql
-- for hive
-- format: `show create table my_table`
-- hdfs path: `describe formatted my_table`

load label user_behavior
(
data infile("hdfs://<hdfs_ip>:<hdfs_port>/user/amber/part-*")
into table user_behavior
columns terminated by "\\x01"
format as "parquet"
({`c1`, `c2`})
set (`c3` = null, `c4` = 0)
where tenant_id = 0
)
with broker 'hdfs_broker'
(
"hadoop.security.authentication" = "simple",
"username" = "<hdfs_username>",
"password" = "<hdfs_password>"
-- "hadoop.security.authentication" = "kerberos",
-- "kerberos_principal" = "user@realm",
-- "kerberos_keytab" = "user.keytab"
)
properties
(
"timeout" = "72000"
);
```

```sql
select * from information_schema.loads where label = 'user_behavior';

show load where label = 'user_behavior';

select * from user_behavior limit 10;
```
