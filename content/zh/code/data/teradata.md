
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
CREATE DATABASE HR
AS PERMANENT = 60e6, -- 60MB
SPOOL = 120e6; -- 120MB

CREATE SET TABLE HR.Employees (
GlobalID INTEGER,
FirstName VARCHAR(30),
LastName VARCHAR(30),
DateOfBirth DATE FORMAT 'YYYY-MM-DD',
JoinedDate DATE FORMAT 'YYYY-MM-DD',
DepartmentCode BYTEINT
)
UNIQUE PRIMARY INDEX ( GlobalID );
INSERT INTO HR.Employees (
GlobalID,
FirstName,
LastName,
DateOfBirth,
JoinedDate,
DepartmentCode
)
VALUES (
101,
'Adam',
'Tworkowski',
'1980-01-05',
'2004-08-01',
01
);

SELECT * FROM HR.Employees;
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