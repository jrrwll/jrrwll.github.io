
---
title: "StarRocks Audit"
---

## info

```sql
show processlist
```

## query

```sql
select * from starrocks_audit_tbl__ where queryType = 'slow_query' order by timestamp desc limit 100
```
