---
title: "Oracle"
---

## install

### container

#### https://container-registry.oracle.com/

```shell
docker login container-registry.oracle.com
# use your orcale username password here

# 19.3.0.0 12.1.0.2
docker pull container-registry.oracle.com/database/enterprise:21.3.0.0

# 5500
# -e ORACLE_SID=ORCLCDB
# -e ORACLE_PDB=ORCLPDB1
# -e ORACLE_PWD=root, SYS, SYSTEM and PDBADMIN password
# -v /opt/oracle/oradata
docker run -d --name oracle \
-p 1521:1521 \
container-registry.oracle.com/database/enterprise:21.3.0.0
```

```shell
# sqlplus / as sysdba
docker exec -it oracle sqlplus / as sysdba
docker exec -it oracle sqlplus sys/<password>@<SID> as sysdba
docker exec -it oracle sqlplus system/<password>@<SID>
docker exec -it oracle sqlplus pdbadmin/<password>@<PDBname>

sqlplus sys/<password>@//localhost:1521/<SID> as sysdba
sqlplus system/<password>@//localhost:1521/<SID>
sqlplus pdbadmin/<password>@//localhost:1521/<PDBname>

# Changing the Default Password for SYS User
docker exec oracle ./setPassword.sh <new_password>
```

#### https://hub.docker.com/r/epiclabs/docker-oracle-xe-11g
```shell
# -v /u01/app/oracle
docker run -d --name oracle11g \
-e ORACLE_ALLOW_REMOTE=true \
-e ORACLE_PASSWORD=root \
-p 1522:1521 \
epiclabs/docker-oracle-xe-11g
```

```sql
-- user: system
-- password: ORACLE_PASSWORD=oracle
conn sys/root@xe as sysdba
```

#### https://hub.docker.com/r/gvenzl/oracle-xe
```shell
# -e ORACLE_PASSWORD
# -v /u01/app/oracle/oradata
docker run -d --name oracle11g \
-e APP_USER=myuser \
-e APP_USER_PASSWORD=mypassword \
-p 1522:1521 \
gvenzl/oracle-xe:11
docker exec oracle11g resetPassword new_password
```


## sql

```sql
select sysdate from dual;
select * from v$version;
select name from v$database;
select instance_name, status from v$instance;

select default_tablespace, temporary_tablespace, d.username
from dba_users d;
select username, default_tablespace from dba_users;
```

```sql
create tablespace mytablespace
datafile 'mytablespace.df'
size 1m;

select table_name, tablespace_name, owner from dba_tables
where tablespace_name = 'mytablespace'
order by owner, table_name;
```

```sql
-- sqlplus '/ as sysdba'

# switch to PDB rather than CDB
# ORCLPDB1
show pdbs;
-- alter pluggable database ORCLPDB1 open;
alter session set container=ORCLPDB1;

show user
create user myuser identified by mypassword;
grant sysdba to myuser;
grant create session to myuser;
select table_name, tablespace_name, owner from dba_tables
where owner = 'myuser'
order by tablespace_name, table_name;
```
