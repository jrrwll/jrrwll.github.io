
---
title: "Apache Guacamole"
---

## intro

## install

### container

**server**

```shell
# -p 4822
docker run --name guacd -d guacamole/guacd
# create a db for guacamole_user
docker run -itd --name postgres \
    -p 5432:5432 \
    -e POSTGRES_PASSWORD=postgres \
    -e PGDATA=/var/lib/postgresql/data/pgdata \
    -v ${PWD}/postgres:/var/lib/postgresql/data \
    postgres

docker exec -it postgres psql -U postgres -d postgres
```

**config db**

```sql
create user guacamole_user with password 'guacamole_user';
create database guacamole_db owner guacamole_user;
grant all on database guacamole_db to guacamole_user;
exit
```

```shell
docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --postgresql > initdb.sql
docker cp ./initdb.sql postgres:/
docker exec -it postgres psql -U guacamole_user -d guacamole_db
```

```sql
-- exec initdb.sql via user guacamole_user
\i /initdb.sql
exit
```

```shell
sudo sed -i 's/host all all all scram-sha-256/host all all all trust/' postgres/pgdata/pg_hba.conf
docker restart postgres
```

**client**

```shell
docker run -itd --name guacamole \
    --link guacd:guacd \
    --link postgres \
    -e POSTGRES_DATABASE=guacamole_db \
    -e POSTGRES_HOSTNAME=postgres \
    -e POSTGRES_USER=guacamole_user \
    -e POSTGRES_PASSWORD=guacamole_user \
    -p 8080:8080 guacamole/guacamole    
```

http://x.x.x.x:8080/guacamole/
guacadmin
guacadmin

settings-connections
http://x.x.x.x:8080/guacamole/#/settings/postgresql/connections

