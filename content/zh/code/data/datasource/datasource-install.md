
---
title: "Datasource Install"
---

## container

```shell
DATASOURCE_PASSWORD=
LOCAL_PATH=$(cd . && pwd -P)
cp datasource-template.yaml datasource.yaml
sed -i "s/\${PASSWORD}/$DATASOURCE_PASSWORD/g" datasource.yaml
sed -i "s/\${LOCAL_PATH}/$LOCAL_PATH/g" datasource.yaml

docker-compose -f datasource.yaml up -d
# own the LOCAL_PATH for current user
sudo chown -R $(id -u -n):$(id -g -n) $LOCAL_PATH
# uid 10000 for pulsar
sudo chown -R 10000:$(id -g -n) pulsar_300_data
```
