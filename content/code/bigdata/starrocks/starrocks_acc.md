
---
title: "Query Acceleration of StarRocks"
---

## query

### 排序聚合 Sorted streaming aggregate

> https://docs.starrocks.io/docs/using_starrocks/sorted_aggregate

可以根据输入 key 的有序性，通过直接比较 GROUP BY 列的方式直接进行分组，不需要构建 hash 表，可以有效的减少聚合计算中的内存使用。在聚合基数比较高的情况下，可以提升聚合性能，降低内存使用。

```sql
set enable_sort_aggregate=true;

-- AGGREGATE
-- sorted streaming: true
explain costs select xxx group by xxx;
```

## update

### 列模式的部分更新 3.1+

```sql
select @@partial_update_mode;
set partial_update_mode = 'column'
```
