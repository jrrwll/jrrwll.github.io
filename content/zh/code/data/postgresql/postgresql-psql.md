
---
title: "psql"
---

## psql

```shell
psql -h 127.0.0.1 -p 5432 -U postgres
```

```sql
-- show client_encoding;
-- set client_encoding = 'utf-8';
\encoding

-- \h select 
\h
-- list cmds
\?

-- show databases
\l
\l+
-- connect db
\c my_db
-- show tables
\d
\dt
\dt+
-- desc tname
\d my_table
-- show tablespaces
\db

-- show users
\du
\dg

-- execute file
\i my_file
```

## cli

```shell
createdb mydb
dropdb mydb
```
