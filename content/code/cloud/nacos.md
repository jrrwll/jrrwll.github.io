
---
title: "Nacos"
---

## install

### standalone

```shell
# -v ./application.properties:/home/nacos/conf/application.properties
# -v ./logs:/home/nacos/logs
docker run -d --name=nacos \
    --link mysql57 \
    -e PREFER_HOST_MODE=hostname -e MODE=standalone \
    -e SPRING_DATASOURCE_PLATFORM=mysql \
    -e MYSQL_SERVICE_HOST=$(docker ps -aqf 'name=mysql57') \
    -e MYSQL_SERVICE_PORT=3306 \
    -e MYSQL_SERVICE_USER=nacos -e MYSQL_SERVICE_PASSWORD=nacos \
    -e MYSQL_SERVICE_DB_NAME=nacos \
    -p 8848:8848 \
    -p 9848:9848 \
    nacos/nacos-server

# 在启动nacos之前需要先在mysql上执行DDL
# https://raw.githubusercontent.com/alibaba/nacos/develop/distribution/conf/mysql-schema.sql
docker exec -it mysql57 mysql -uroot -proot -e 'create database if not exists nacos'
docker cp ./mysql-schema.sql mysql57:/docker-entrypoint-initdb.d/nacos-mysql.sql
docker exec -it mysql57 bash -c 'source /entrypoint.sh && docker_process_init_files /docker-entrypoint-initdb.d/nacos-mysql.sql'
```

### cluster

```yaml

```
