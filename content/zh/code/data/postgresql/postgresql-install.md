
---
title: "PostgreSQL Install"
---

## install

### linux

```shell
sudo apt install postgresql-client postgresql pgadmin3
```

### mac

```shell
brew install postgresql
brew services start postgresql
```

### docker

```shell
# pg_hba.conf, postgresql.conf
mkdir pg_data
cat <<EOF > pg_data/pg_hba.conf
# host database user address method
host all all 0.0.0.0/0 md5
host all all ::/0 md5
EOF

# -e POSTGRES_USER=postgres \
# -e POSTGRES_DB=postgres \
docker run -d -it --name postgres15 \
  -e POSTGRES_PASSWORD=postgres \
  -e PGDATA=/var/lib/postgresql/data/pgdata \
  -v ${PWD}/pg_data:/var/lib/postgresql/data \
 -p 5432:5432 \
 postgres:15

docker exec -it postgres15 psql -hlocalhost -U postgres
```

```shell
# POSTGRES_HOST_AUTH_METHOD: md5, trust, scram-sha-256
# echo "host all all all $POSTGRES_HOST_AUTH_METHOD" >> pg_hba.conf
docker run -d -it --name postgres15 \
-e POSTGRES_PASSWORD=postgres \
-e POSTGRES_HOST_AUTH_METHOD=md5 \
-p 5432:5432 \
postgres:15
```

## config

### `/var/lib/postgresql/data/pg_hba.conf`

```conf
# 开放所有网段的客户端密码连接
# host	database	user	address		method
host    all             all              0.0.0.0/0                       md5
host    all             all              ::/0                            md5
```

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

### `/var/lib/postgresql/data/postgresql.conf`

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

```conf
# 监听所有IP	
listen_addresses  = '*'
# 日志的收集
logging_collector = on 
# 定义日志的收集目录
log_directory     = 'pg_logs' 

# 方案一：每天生产一个新的日志文件
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
log_truncate_on_rotation = off
log_rotation_age = 1d
log_rotation_size = 0
# 方案二：每当日志写满一定的大小（如10MB空间），则切换一个日志
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
log_truncate_on_rotation = off
log_rotation_age = 0
log_rotation_size = 10M
# 方案三：只保留7天的日志，进行循环覆盖 
log_filename = 'postgresql-%a.log'
log_truncate_on_rotation = off
log_rotation_age = 1d
log_rotation_size = 0
```
