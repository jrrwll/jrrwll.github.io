
---
title: "StarRocks Audit"
---

## info

```sql
show processlist
```

## query_profile

```sql
select get_query_profile(last_query_id());
```

## query

```sql
select * from starrocks_audit_tbl__ where queryType = 'slow_query' order by timestamp desc limit 100
```
