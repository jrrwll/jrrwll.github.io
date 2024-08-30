
---
title: "Variables"
---

```sql

-- https://docs.starrocks.io/zh/docs/reference/System_variable
show variables like 'query_mem_limit'
-- 128g
set query_mem_limit = 137438953472;
-- 设置全局参数
set global query_mem_limit = 137438953472;


-- set_var hint
select /*+ set_var(query_mem_limit = 515396075520, query_timeout = 10000000) */ name from people order by name;


-- 数据库配额
alter database example_db set data quota 10t;
```
