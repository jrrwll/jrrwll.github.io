---
title: "StarRocks Repair"
---

## Replica

```sql
-- 查看所有副本的状态
show proc '/statistic';
```

- **set bad**
```sql
admin set replica status properties(
    "tablet_id" = "${tablet_id}",
    "backend_id" = "${backend_id}",
    "status" = "bad"
);
```

- **repair partitions**
```sql
admin repair table table_name partition (p1, p2);
```

- **swap with temporary partition**
```sql
alter table table_name
add temporary partition bak202401 values [("2024-01-01"), ("2024-02-01")];

alter table table_name
add temporary partition start ("2024-01-01") end ("2024-02-01") every (interval 1 month);

insert into table_name temporary partition bak202401
select * from table_name where event_date >= "2024-01-01" and event_date < "2024-02-01";

alter table table_name
replace partition (p202401) with temporary partition (tp202401)
properties ("strict_range" = "true");
```

## Tablet

```sql
show tablet $tablet_id;

select * from events tabelet($tablet_id);
```
