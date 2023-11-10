
---
title: "ClickHouse JDBC"
---

## settings

### prefer_column_name_to_alias
https://github.com/ClickHouse/ClickHouse/pull/22044

```sql
SELECT avg(number) AS number, max(number) FROM numbers(10) settings prefer_column_name_to_alias  = 1;
```
