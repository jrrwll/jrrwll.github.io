
---
title: "PostgreSQL"
---

## install

### container

```shell
# -e POSTGRES_USER=postgres \
# -e POSTGRES_DB=postgres \
docker run -d -it --name postgres15 \
 -e POSTGRES_PASSWORD=postgres \
 -v ${PWD}/postgresql.conf:/var/lib/postgresql/data/postgresql.conf \
 -v ${PWD}/pg_hba.conf:/var/lib/postgresql/data/pg_hba.conf \
 -v ${PWD}/data:/var/lib/postgresql/data \
 -p 5432:5432 \
 postgres:15

docker exec -it postgres15 psql -hlocalhost -U postgres
```

## config

### config file

#### `/var/lib/postgresql/data/pg_hba.conf`

```conf
local   all             all                                     trust
host    all             all             127.0.0.1/32            trust
host    all             all             ::1/128                 trust
local   replication     all                                     trust
host    replication     all             127.0.0.1/32            trust
host    replication     all             ::1/128                 trust
host   all           all            0.0.0.0/0              trust
```

```conf
local      database  user  auth-method  [auth-options]
host       database  user  CIDR-address  auth-method  [auth-options]
hostssl    database  user  CIDR-address  auth-method  [auth-options]
hostnossl  database  user  CIDR-address  auth-method  [auth-options]
host       database  user  IP-address  IP-mask  auth-method  [auth-options]
hostssl    database  user  IP-address  IP-mask  auth-method  [auth-options]
hostnossl  database  user  IP-address  IP-mask  auth-method  [auth-options]
```

| 字段         | 值/含义                                                      |
| ------------ | ------------------------------------------------------------ |
| local        | Unix域套接字                                                 |
| host         | TCP/IP，匹配SSL和非SSL的连接                                 |
| hostssl      | TCP/IP，仅匹配SSL的连接                                      |
| hostnossl    | TCP/IP，仅匹配非SSL的连接                                    |
| database     | 匹配数据库：all 匹配所有；sameuser 用户同名；samerole 角色同名；<br/>replication 匹配复制连接请求；<br/>a,b,c 逗号分隔的具体数据库名；@/a/b/c 包含数据库名的文件 |
| user         | 匹配用户：all 匹配所有；a,b,c 逗号分割的特定用户或组；<br/>+a,b,c 逗号分割的特定组，加号意味着匹配任何直接或者间接属于这个角色的成员；<br/>@/a/b/c 包含用户名的文件 |
| CIDR-address | 匹配客户端IP地址范围                                         |
| auth-method  | 认证方法：trust 允许连接；reject 拒绝连接；md5 MD5加密口令认证；<br/>krb5 Kerberos V5 认证；gss GSSAPI认证；cert SSL客户端证书认证 |
| auth-options | name=value键值对                                             |

​	

#### `/var/lib/postgresql/data/postgresql.conf`

```conf
listen_addresses = '*'
#port = 5432    # (change requires restart)
max_connections = 100
shared_buffers = 128MB
dynamic_shared_memory_type = posix
max_wal_size = 1GB
min_wal_size = 80MB
log_timezone = 'Etc/UTC'
datestyle = 'iso, mdy'
timezone = 'Etc/UTC'
lc_messages = 'en_US.utf8'
lc_monetary = 'en_US.utf8'
lc_numeric = 'en_US.utf8'
lc_time = 'en_US.utf8'
default_text_search_config = 'pg_catalog.english'
```

## sql

### info

```sql
select version();
select current_date;

select name, setting from pg_settings where category = 'File Locations' ;
```

### auth

```sql
create user myuser with password 'secret'; 

grant all privileges on database postgres to postgres;
alter schema public owner to postgres;
grant all privileges on all sequences in schema public to postgres;
grant all privileges on all tables in schema public to postgres;
```

### ddl

```shell
# /usr/local/pgsql/bin/createdb
createdb mydb
dropdb mydb

```