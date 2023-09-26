---
title: "Oracle"
---

## install

### container

#### https://container-registry.oracle.com/

```shell
docker login container-registry.oracle.com
# use your orcale username password here

# -p 5500:5500
# -e ORACLE_PWD: SYS, SYSTEM and PDB_ADMIN password
# -e ORACLE_CHARACTERSET: AL32UTF8
# -v /opt/oracle/oradata
docker run -itd --name oracle21xe \
  -e ORACLE_PWD=oracle \
  -p 1521:1521 \
  -v $PWD/oracle21xe:/opt/oracle/oradata \
  container-registry.oracle.com/database/express:21.3.0-xe

# -e ORACLE_SID=ORCLCDB
# -e ORACLE_PDB=ORCLPDB1
docker run -d --name oracle21 \
-p 1521:1521 \
-v $PWD/oracle21:/opt/oracle/oradata \
container-registry.oracle.com/database/enterprise:21.3.0.0
```

```shell
# sqlplus / as sysdba
docker exec -it oracle sqlplus / as sysdba
docker exec -it oracle sqlplus sys/<password>@<SID> as sysdba
docker exec -it oracle sqlplus system/<password>@<SID>
docker exec -it oracle sqlplus pdbadmin/<password>@<PDBname>

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

#### [gvenzl/oracle-xe](https://hub.docker.com/r/gvenzl/oracle-xe)

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

#### [deepdiver/docker-oracle-xe-11g](https://hub.docker.com/r/deepdiver/docker-oracle-xe-11g)

```shell
# 账号密码 sys/system oracle sid=xe
# autotest owncloud
# -p 1022:22 系统账号密码 root admin
# -p 8080:8080
docker run -itd --name oracle11g \
-p 1521:1521 \
deepdiver/docker-oracle-xe-11g

docker exec -it oracle11g sqlplus system/oracle
```

## jdbc

jdbc:oracle:thin:@host:port:sid sid=instance_name

jdbc:oracle:thin:@//host:port/sn sn=service_names

```sql
select name, value from v$parameter where name in ('instance_name', 'service_names');
```

## sql

### auth

```sql
show parameter service

-- user
select username,password from dba_users;
create user myuser identified by Mypassword_123;
grant connect,resource to myuser;
-- docker exec -it oracle11g sqlplus myuser/Mypassword_123

-- tablespace
create tablespace mytablespace datafile 'mytablespace.dbf' size 10240M;
alter user myuser default tablespace mytablespace;
select tablespace_name from user_tablespaces;
```

### sqlplus

```shell
su - oracle
sqlplus / as sysdba
sqlplus '/ as sysdba'

sqlplus sys/<password>@//localhost:1521/<SID> as sysdba
sqlplus system/<password>@//localhost:1521/<SID>
sqlplus pdbadmin/<password>@//localhost:1521/<PDBname>
```

### info

```sql
select sysdate from dual;
select * from v$version;
select name from v$database;
select instance_name, status from v$instance;

-- show create table <user>.<table>
select dbms_metadata.get_ddl('TABLE','MY_TABLE','MY_USER') from dual;
```

### tablespace

```sql
create tablespace mytablespace
datafile 'mytablespace.df'
size 1m;

select table_name, tablespace_name, owner from dba_tables
where tablespace_name = 'mytablespace'
order by owner, table_name;
```

### user

```sql
-- switch to PDB rather than CDB
-- ORCLPDB1
show pdbs;
-- alter pluggable database ORCLPDB1 open;
alter session set container=ORCLPDB1;

-- sqlplus myuser/mypassword@ORCLPDB1
show user
create user myuser identified by mypassword;
grant sysdba to myuser;
grant create session to myuser;

select username, default_tablespace from dba_users where username = 'MYUSER';
alter user myuser default tablespace mytablespace;
alter user myuser quota unlimited on mytablespace;
```

```sql
select table_name, tablespace_name, owner from dba_tables
where owner = 'MYUSER'
order by tablespace_name, table_name;
```

### insert all

```sql
create table t_num(c number);

insert all
into t_num values(1)
into t_num values(3.14)
into t_num values(0.000000000014)
select 1 from dual;
```
