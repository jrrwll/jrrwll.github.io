
---
title: "PostgreSQL Auth"
---

## info

```sql
select version();
select current_date;

select name, setting from pg_settings where category = 'File Locations' ;
```

## auth

```sql
-- sudo adduser myuser
-- sudo passwd myuser
create user myuser with password 'mypassword'; 
alter role myuser with password 'mypassword';

create database testdb;
alter database testdb owner to myuser;
grant all privileges on database testdb to myuser;
alter schema public owner to myuser;
grant all privileges on all sequences in schema public to myuser;
grant all privileges on all tables in schema public to myuser;
-- schema
create schema test;
alter schema public owner to myuser;

```

```sql
-- select * from pg_roles;
create role kight;
alter role kight with password 'kight';
alter role kight login;

create database kight 
with owner=kight template=template0 
encoding='UTF8' connection limit = 100;

alter database kight set tablespace pg_default;
alter database kight reset client_encoding;

alter role kight set default_transaction_read_only=on;
grant all privileges on database kight to kight;
grant select on all tables in schema public to kight;

drop database if exists kight;
```

## ddl

```sql
-- 重命名表
alter table my_table rename to my_table_backup
-- 重命名列
alter table my_table rename column col1 to col2;
```

## backup & restore

```sql
-- 导出 CSV
copy my_table to '/path/to/output.csv' with csv;
copy my_table(col1, col2) to '/path/to/output.csv' with csv;
copy (
    select col1, col2 from my_table
) to '/path/to/output.csv' with csv;

-- 导入 CSV
copy my_table from '/path/to/input.csv' with csv;
-- HEADER 声明文件包含一个标题头行，包含文件中每个字段的名字
-- QUOTE 声明 CSV 模式里的引号字符, 缺省是双引号
copy my_table from '/path/to/input.csv'
with delimiter ',' null as '' csv header quote as '|'

copy (select * from my_table) to stdout;
copy (select * from my_table) from 'command';
```
