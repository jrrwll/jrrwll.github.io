
---
title: "MySQL Bench"
---

## QPS & TPS
> Queries Per Second
> Transactions Per Second

```sql
show global status;
show status;
```

- **Method 1** for myisam

```sql
show global status like 'questions';
show global status like 'uptime';
-- qps = questions / uptime

show global status like 'com_commit';
show global status like 'com_rollback';
-- tps = (com_commit + com_rollback) / uptime
```

- **Method 2** for innodb

```sql
show global status where variable_name in('
com_select','com_insert','com_delete','com_update');
```

## processlist

```sql
show full processlist;
```
