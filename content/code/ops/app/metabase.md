
---
title: "metabase"
---

## intro
https://github.com/metabase/metabase/releases

## install

https://www.metabase.com/docs/latest/installation-and-operation/running-metabase-on-docker

```shell
docker run -d --name metabase \
    -p 3000:3000 \
    metabase/metabase

docker run -d --name metabase \
  -p 3000:3000 \
  -e "MB_DB_TYPE=postgres" \
  -e "MB_DB_DBNAME=metabase" \
  -e "MB_DB_PORT=5432" \
  -e "MB_DB_USER=name" \
  -e "MB_DB_PASS=password" \
  -e "MB_DB_HOST=my-database-host" \
  metabase/metabase

  -e "MB_DB_TYPE=mysql"
  -e "MB_DB_CONNECTION_URI=jdbc:mysql://<host>:3306/metabase?user=<username>&password=<password>"
```
