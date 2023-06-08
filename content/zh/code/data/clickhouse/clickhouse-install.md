
---
title: "Install ClickHouse"
---

## container

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

### bitnami
> https://hub.docker.com/r/bitnami/clickhouse

```shell
docker run -it -d --name clickhouse \
  -e ALLOW_EMPTY_PASSWORD=no \
  -e CLICKHOUSE_ADMIN_USER=root \
  -e CLICKHOUSE_ADMIN_PASSWORD=root \
  -p 8123:8123 \
  bitnami/clickhouse:23
```

```shell
# docker pull bitnami/clickhouse
curl -sSL https://raw.githubusercontent.com/bitnami/containers/main/bitnami/clickhouse/docker-compose.yml > docker-compose.yml
docker-compose up -d
```

```yaml
version: '2'
services:
  clickhouse:
    image: docker.io/bitnami/clickhouse:23
    environment:
      - ALLOW_EMPTY_PASSWORD=yes
    ports:
      - '8123:8123'
    volumes:
      - clickhouse_data:/bitnami/clickhouse
volumes:
  clickhouse_data:
    driver: local
```
