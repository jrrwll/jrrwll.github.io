---
title: "Nebula"
---

## install

> https://docs.nebula-graph.com.cn/3.8.0/


```shell
git clone -b release-3.8 https://github.com/vesoft-inc/nebula-docker-compose.git
cd nebula-docker-compose
docker-compose up -d

docker exec -it nebula-docker-compose_console_1 /bin/sh
./usr/local/bin/nebula-console -u root -p root --address=graphd --port=9669
```

## sql

```sql
show hosts;
```
