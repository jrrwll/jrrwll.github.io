
---
title: "GreenPlum"
---

## install

```shell
# root/pivotal gpadmin/pivotal
docker run -it -d --name greenplum \
    -e GP_USER=myuser \
    -e GP_PASSWORD=mypassword \
    -e GP_DB=testdb \
    -p 5432:5432 \
    --privileged=true \
    datagrip/greenplum:6.8

su - gpadmin
# https://gp-docs-cn.github.io/docs/utility_guide/client_utilities/psql.html
psql
```

```sql
-- 查看所有表
select * from pg_tables;
select tablename from pg_tables where schemaname = 'public';

\d my_table;
```
