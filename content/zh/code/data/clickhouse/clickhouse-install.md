
---
title: "Install ClickHouse"
---

## docker

```shell
docker run -d --name clickhouse \
    --ulimit nofile=262144:262144 \
    -p 8123:8123 -p9000:9000 \
    -e CLICKHOUSE_DB=test \
    -e CLICKHOUSE_USER=root -e CLICKHOUSE_PASSWORD=root \
    -e CLICKHOUSE_DEFAULT_ACCESS_MANAGEMENT=1 \
    clickhouse/clickhouse-server

echo 'SELECT version()' | curl 'http://localhost:18123/' --data-binary @-
```
