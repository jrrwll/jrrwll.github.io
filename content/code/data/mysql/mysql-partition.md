
---
title: "MySQL Partition"
---

## partition types

### range

```sql
create table table_using_range_partition_1(
    id int
) partition by range (id) (
    partition p0 values less than (-1), -- x < -1
    partition p1 values less than (1), -- -1 <= x < 1
    partition p2 values less than maxvalue -- x >= 1
);
insert into table_using_range_partition_1 values (-1);
insert into table_using_range_partition_1 values (1);

select * from information_schema.PARTITIONS
where TABLE_NAME = 'table_using_range_partition_1';
```

```sql
-- year, to_days, to_seconds, unix_timestamp
create table table_using_range_partition_2(
    create_time datetime
) partition by range (to_days(create_time)) (
    partition p0 values less than (to_days('2020-10-01')),
    partition p1 values less than (to_days('2020-11-01')),
    partition p2 values less than (to_days('2020-12-01'))
);

-- 对非整型不用使用转换函数, date, datetime, char, varchar, binary, varbinary
create table table_using_range_columns_partition_1(
    create_time datetime
) partition by range columns (create_time) (
    partition p0 values less than ('2020-01-01')
);

create table table_using_range_columns_partition_2(
    a int,
    b date,
    c varchar(30)
) partition by range columns (a, b, c) (
    partition p0 values less than (1, '2020-01-01', 'alpha')
);
```

### list

```sql
create table table_using_list_partition(
    type int
) partition by list (type) (
    partition p0 values in (0),
    partition p1 values in (1, 2),
    partition p2 values in (3, 4, 5)
);
```

### hash

```sql
create table table_using_hash_partition_1(
    value int
) partition by hash (value) partitions 4;

insert into table_using_hash_partition_1 values (0); -- p0
insert into table_using_hash_partition_1 values (1); -- p1
insert into table_using_hash_partition_1 values (2); -- p2
insert into table_using_hash_partition_1 values (3); -- p3

select PARTITION_NAME, PARTITION_METHOD, TABLE_ROWS, AVG_ROW_LENGTH
from information_schema.PARTITIONS
where TABLE_NAME = 'table_using_hash_partition_1';
```

```sql
create table table_using_hash_partition_2(
    value int
) partition by linear hash (value) partitions 5;

-- 取小于等于分区大小的最小的一个2的幂值，即v=4,  则分区号为 数值对幂值的模，即 n & (v - 1)
insert into table_using_hash_partition_2 values (0); -- p0
insert into table_using_hash_partition_2 values (1); -- p1
insert into table_using_hash_partition_2 values (2); -- p2
insert into table_using_hash_partition_2 values (3); -- p3
insert into table_using_hash_partition_2 values (4); -- p4
insert into table_using_hash_partition_2 values (5); -- p1
insert into table_using_hash_partition_2 values (6); -- p2
insert into table_using_hash_partition_2 values (7); -- p3
insert into table_using_hash_partition_2 values (8); -- p0

select PARTITION_NAME, PARTITION_METHOD, TABLE_ROWS, AVG_ROW_LENGTH
from information_schema.PARTITIONS
where TABLE_NAME = 'table_using_hash_partition_2';
```

### key

```sql
-- 取小于等于分区大小的最小的一个2的幂值的模
create table table_using_key_partition(
    create_time int
) partition by key (create_time) partitions 4;
```
