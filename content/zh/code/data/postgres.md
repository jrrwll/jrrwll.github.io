
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

```sql
GRANT ALL PRIVILEGES ON DATABASE postgres to postgres;
ALTER SCHEMA public OWNER to postgres;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO postgres;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO postgres;

select name, setting from pg_settings where category = 'File Locations' ;
```

##### `/var/lib/postgresql/data/pg_hba.conf`

```conf
local   all             all                                     trust
host    all             all             127.0.0.1/32            trust
host    all             all             ::1/128                 trust
local   replication     all                                     trust
host    replication     all             127.0.0.1/32            trust
host    replication     all             ::1/128                 trust
host   all           all            0.0.0.0/0              trust
```

##### `/var/lib/postgresql/data/postgresql.conf`

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
