
---
title: "TiDB Install"
---

## container

```shell
PROJECT_NAME=datasource
TIDB_VERSION=v6.5.2
TIDB_COMPACT_VERSION=652
TIDB_NETWORK=$PROJECT_NAME
cp tidb-template.yaml tidb.yaml
sed -i "s/\${TIDB_VERSION}/$TIDB_VERSION/g" tidb.yaml
sed -i "s/\${TIDB_COMPACT_VERSION}/$TIDB_COMPACT_VERSION/g" tidb.yaml
sed -i "s/\${TIDB_NETWORK}/$TIDB_NETWORK/g" tidb.yaml

docker network create $TIDB_NETWORK
docker volume create ${PROJECT_NAME}_tidb_${TIDB_COMPACT_VERSION}_conf
docker volume create ${PROJECT_NAME}_tidb_${TIDB_COMPACT_VERSION}_data
docker volume create ${PROJECT_NAME}_tidb_${TIDB_COMPACT_VERSION}_logs

TIDB_CONF_VOL_DIR=`docker volume inspect ${PROJECT_NAME}_tidb_${TIDB_COMPACT_VERSION}_conf -f '{{.Mountpoint}}'`
sudo cp tidb.toml $TIDB_CONF_VOL_DIR
sudo cp tikv.toml $TIDB_CONF_VOL_DIR
sudo cp pd.toml $TIDB_CONF_VOL_DIR

docker-compose -p $PROJECT_NAME -f tidb.yaml up -d
```
