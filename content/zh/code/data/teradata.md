
---
title: "Teradata Express"
---

## install

### vm

#### mac x86/arm
> UTM
https://quickstarts.teradata.com/getting.started.utm.html

username/password: root/root
start `Gnome Terminal`

launch `Teradata Studio Express`
username/password: dbc/dbc
```shell
pdestate
watch pdestate -a
```

## sql

### exmple

```sql
create database test
as permanent = 60e6, -- 60MB
spool = 120e6; -- 120MB

create table test.t_all_type (
c_id bigint not null generated always as identity (start with 500 increment by 1 no cycle),

c_byteint byteint,
c_smallint smallint,
c_integer integer,
c_bigint bigint,
c_float float,
c_decimal decimal(16,6),
c_number number,

c_char char,
c_varchar varchar(100),
c_long_varchar long varchar,
c_graphic graphic,
c_vargraphic vargraphic(100),
c_long_vargraphic long vargraphic,

c_date date format 'yyyy-mm-dd',
c_time time,
c_timestamp timestamp,
c_timestamp_with_time_zone timestamp with time zone,
c_time_with_time_zone time with time zone,

c_byte byte,
c_varbyte varbyte(100),
c_blob blob,
c_clob clob,
c_xml xml
) unique primary index ( c_id );
```

```sql
insert into test.t_all_type
(c_byteint, c_smallint, c_integer, c_bigint, c_float, c_decimal, c_number,
c_char, c_varchar, c_long_varchar,
c_date)
values (1, 20, 300, 4000, 1.2, 3.14, 55,
'a', 'HBO', 'ShowTime',
'2001-02-03');
```

## client

### java
> https://teradata-docs.s3.amazonaws.com/doc/connectivity/jdbc/reference/current/frameset.html
> https://cdn.cdata.com/help/OTG/jdbc/pg_connectionj.htm

```
# TMODE: the transaction mode
# CHARSET: session character set, default is ASCII
jdbc:teardata://x.x.x.x/DBS_PORT=1025,TMODE=ANSI,
```

### python
> https://github.com/Teradata/python-driver

```python
# pip install teradatasql
import teradatasql

with teradatasql.connect ('{"host":"localhost","user":"guest","password":"please"}') as con:
    with con.cursor () as cur:
        cur.execute ("create volatile table voltab (c1 integer, c2 varchar(100)) on commit preserve rows")
        # batch insert
        cur.execute ("insert into voltab (?, ?)", [
            [1, "abc"],
            [2, "def"],
            [3, "ghi"]])
        cur.execute ("select * from voltab order by 1")
        [ print (row) for row in cur.fetchall () ]
```
