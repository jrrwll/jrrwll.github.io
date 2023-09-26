
---
title: "Hive SQL"
---

## hive

```shell
hive -f some.sql
```

```sql
source /path/to/some.sql
```

### local

```sql
create table test_load_data(c_int int, c_decimal decimal(16, 6), c_string string)
partitioned by (p_date date)
row format delimited fields terminated by ',';

-- or: overwrite into
load data local inpath 'test_load_data.csv'
into table test_load_data partition(p = '2023-09-07');

alter table test_load_data change column c_int c_int decimal(16, 6);

```
