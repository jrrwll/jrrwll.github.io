
---
title: "PostgreSQL Partition"
---

## partition types

### range

```sql
create table t_part_range (
c_int int not null,
c_varchar varchar(100),
c_uuid uuid not null,
c_date date
) partition by range (c_int);

create table t_part_range0 partition of t_part_range for values from (minvalue) to (9);
create table t_part_range1 partition of t_part_range for values from (10) to (19);
create table t_part_range2 partition of t_part_range for values from (20) to (29);
create table t_part_range3 partition of t_part_range for values from (30) to (39);
create table t_part_range4 partition of t_part_range for values from (40) to (49);
create table t_part_range5 partition of t_part_range for values from (50) to (59);
create table t_part_range6 partition of t_part_range for values from (60) to (69);
create table t_part_range7 partition of t_part_range for values from (70) to (maxvalue);
```

### hash

```sql
create table t_part_hash (
c_int int not null,
c_varchar varchar(100),
c_uuid uuid not null,
c_date date
) partition by hash (c_int);

create table t_part_hash0 partition of t_part_hash for values with (modulus 4, remainder 0);
create table t_part_hash1 partition of t_part_hash for values with (modulus 4, remainder 1);
create table t_part_hash2 partition of t_part_hash for values with (modulus 4, remainder 2);
create table t_part_hash3 partition of t_part_hash for values with (modulus 4, remainder 3);
```

### list

```sql
create table t_part_list (
c_int int not null,
c_varchar varchar(100),
c_uuid uuid not null,
c_date date
) partition by list (c_int);

create table t_part_list0 partition of t_part_list for values in (0);
create table t_part_list1 partition of t_part_list for values in (1, 2, 3);
create table t_part_list2 partition of t_part_list default;
```

## meta

```sql
/*
 column_name | data_type | partstrat
-------------+-----------+-----------
 c_int       | integer   | r

 column_name | data_type | partstrat
-------------+-----------+-----------
 c_int       | integer   | l

 column_name | data_type | partstrat
-------------+-----------+-----------
 c_int       | integer   | h
*/
select
    col.column_name, col.data_type, pt.partstrat
from
    (select
         partrelid,
         unnest(partattrs) column_index,
         partstrat
     from
         pg_partitioned_table) pt
join
    information_schema.columns col
on  ordinal_position = pt.column_index
where pt.partrelid = 'public.t_part_range'::regclass and col.table_name = 't_part_range' and col.table_schema = 'public';
```

```sql
/*
^FOR VALUES IN \((.+?)\)$

 partition_table_name |  partition_expression
----------------------+-------------------------
 t_part_list0         | FOR VALUES IN (0)
 t_part_list1         | FOR VALUES IN (1, 2, 3)
 t_part_list2         | DEFAULT
*/
select
    partition.relname as partition_table_name,
    pg_get_expr(partition.relpartbound, partition.oid) as partition_expression
from
    pg_class AS parent
join
    pg_inherits AS i ON parent.oid = i.inhparent
join
    pg_class AS partition ON i.inhrelid = partition.oid
where
    parent.oid = 'public.t_part_list'::regclass
    and parent.relkind = 'p';
```
