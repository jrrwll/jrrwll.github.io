---
title: "DB2"
---

## install

### container

| Image | Version |
|:-------------------------------------------------------:|:-------:|
| ibmcom/db2 | 11 |
| rodrigotsuru/db2-docker<br/>maketechnology/db2:10.5.0.5 | 10 |
| | |

```shell
docker run -it -d --name db2 --privileged=true \
 -p 50000:50000 \
 -e LICENSE=accept \
 -e DB2INST1_PASSWORD=root \
 -e DBNAME=testdb ibmcom/db2

# DB2INSTANCE variable in your .env_list file
docker exec -it db2 su - db2inst1
# change passwd for os user
# r_o_o_t_s_pd
passwd
```

```shell
db2sampl -sql -name sample
db2 connect to sample
db2 "select * from department" 
db2 "select * from department fetch first 1 rows only"
db2 list database directory

# https://www.ibm.com/support/pages/how-change-db2-user-password
db2 connect to my_database user my_user using
 my_password new my_new_password confirm my_new_password
```

## sql

### 全类型表

```sql
create table t_all_type (
 c_id BIGINT generated always as identity (start with 500, increment by 1),
 c_bigint BIGINT,
 c_long_varchar_for_bit_data LONG VARCHAR FOR BIT DATA,
 c_varchar VARCHAR(100),
 c_char CHAR(32),
 c_long_varchar LONG VARCHAR,
 c_graphic GRAPHIC(10),
 c_decimal DECIMAL(16, 6),
 c_integer INTEGER,
 c_smallint SMALLINT,
 c_real REAL,
 c_double DOUBLE,
 c_vargraphic VARGRAPHIC(100),
 c_boolean BOOLEAN,
 c_date DATE,
 c_time TIME,
 c_timestamp TIMESTAMP,
 c_decfloat DECFLOAT,
 c_xml XML,
 c_blob BLOB,
 c_clob CLOB,
 c_dbclob DBCLOB
) in database sample;

-- https://www.ibm.com/docs/en/db2-for-zos/12?topic=statements-comment
comment on table t_all_type is 'Includes Most Types';

comment on column t_all_type.c_id is 'id column';
comment on column t_all_type.c_bigint is '类型：BIGINT';
comment on column t_all_type.c_long_varchar_for_bit_data is '类型：LONG';
comment on column t_all_type.c_varchar is '类型：VARCHAR';
comment on column t_all_type.c_char is '类型：CHAR';
comment on column t_all_type.c_long_varchar is '类型：LONG';
comment on column t_all_type.c_graphic is '类型：GRAPHIC';
comment on column t_all_type.c_decimal is '类型：DECIMAL';
comment on column t_all_type.c_integer is '类型：INTEGER';
comment on column t_all_type.c_smallint is '类型：SMALLINT';
comment on column t_all_type.c_real is '类型：REAL';
comment on column t_all_type.c_double is '类型：DOUBLE';
comment on column t_all_type.c_vargraphic is '类型：VARGRAPHIC';
comment on column t_all_type.c_boolean is '类型：BOOLEAN';
comment on column t_all_type.c_date is '类型：DATE';
comment on column t_all_type.c_time is '类型：TIME';
comment on column t_all_type.c_timestamp is '类型：TIMESTAMP';
comment on column t_all_type.c_decfloat is '类型：DECFLOAT';
comment on column t_all_type.c_xml is '类型：XML';
comment on column t_all_type.c_blob is '类型：BLOB';
comment on column t_all_type.c_clob is '类型：CLOB';
comment on column t_all_type.c_dbclob is '类型：DBCLOB';
```

```sql
insert into t_all_type(
 c_bigint, c_long_varchar_for_bit_data, c_varchar, c_char, 
 c_long_varchar, c_graphic, c_decimal, c_integer, c_smallint, 
 c_real, c_double, c_vargraphic, c_boolean, c_date, c_time, 
 c_timestamp, c_decfloat, c_xml, c_blob, c_clob, c_dbclob)
values 
(1, 'a', 'varchar_value1', 'char_value1', 
'long_varchar_value1', 'graphic_1', 3.14, 1, 10, 
1.414, 2.718, '', true, '2000-01-01', '01:01:01', now(), 2.10, '<a b="c">d</a >', cast('blob_value1' as BLOB), cast('clob_value1' as CLOB), cast('dbclob_value1' as DBCLOB));

insert into t_all_type( c_bigint, c_long_varchar_for_bit_data, c_varchar, c_char, c_long_varchar, c_graphic, c_decimal, c_integer, c_smallint, c_real, c_double, c_vargraphic, c_boolean, c_date, c_time, c_timestamp, c_decfloat, c_xml, c_blob, c_clob, c_dbclob) values (34, 'bb', 'varchar_value2', 'char_value2', 'long_varchar_value2', 'graphic_2', 4.14, 2, 11, 2.414, 3.718, 'v2', false, '2010-02-01', '01:02:03', now(), 2.50, '<a b2="c">d2</a >', cast('blob_value2' as BLOB), cast('clob_value2' as CLOB), cast('dbclob_value2' as DBCLOB));
insert into t_all_type( c_bigint, c_long_varchar_for_bit_data, c_varchar, c_char, c_long_varchar, c_graphic, c_decimal, c_integer, c_smallint, c_real, c_double, c_vargraphic, c_boolean, c_date, c_time, c_timestamp, c_decfloat, c_xml, c_blob, c_clob, c_dbclob) values (366, 'bcc', 'varchar_value3', 'char_value3', 'long_varchar_value3', 'graphic_3', 40.6, 77, 13, 3.333, 4.8, 'v3', false, '2011-03-01', '03:02:03', now(), 5.10, '<a b3="cc">d3</a >', cast('blob_value3' as BLOB), cast('clob_value3' as CLOB), cast('dbclob_value3' as DBCLOB));
```
