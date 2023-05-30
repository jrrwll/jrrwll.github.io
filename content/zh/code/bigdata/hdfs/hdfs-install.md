
---
title: "HDFS Install"
---

## docker-compose

```shell
HADOOP_VERSION=2.0.0-hadoop3.2.1-java8
HBASE_VERSION=1.0.0-hbase1.2.6
POSTGRESQL_VERSION=2.3.0
HIVE_VERSION=2.3.2-postgresql-metastore
PRESTODB_VERSION=0.245.1
cp hdfs-template.yaml hdfs.yaml
sed -i "s/\${HADOOP_VERSION}/$HADOOP_VERSION/g" hdfs.yaml
sed -i "s/\${HBASE_VERSION}/$HBASE_VERSION/g" hdfs.yaml
sed -i "s/\${POSTGRESQL_VERSION}/$POSTGRESQL_VERSION/g" hdfs.yaml
sed -i "s/\${HIVE_VERSION}/$HIVE_VERSION/g" hdfs.yaml
sed -i "s/\${PRESTODB_VERSION}/$PRESTODB_VERSION/g" hdfs.yaml

PROJECT_NAME=datasource
HDFS_NETWORK=$PROJECT_NAME
sed -i "s/\${HDFS_NETWORK}/$HDFS_NETWORK/g" hdfs.yaml

docker network create $HDFS_NETWORK
docker volume create ${PROJECT_NAME}_hadoop_namenode
docker volume create ${PROJECT_NAME}_hadoop_datanode
docker volume create ${PROJECT_NAME}_hadoop_historyserver
docker volume create ${PROJECT_NAME}_hbase_data
docker volume create ${PROJECT_NAME}_hbase_zookeeper_data

docker-compose -p $PROJECT_NAME -f hdfs.yaml up -d
```
