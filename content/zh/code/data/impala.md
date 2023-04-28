---
title: "Impala"
---

## install

### container
>https://github.com/apache/impala/blob/master/docker/README.md

```shell
mkdir impala && cd impala
wget https://github.com/apache/impala/raw/master/docker/quickstart.yml
wget https://github.com/apache/impala/raw/master/docker/quickstart-kudu-minimal.yml
wget https://github.com/apache/impala/raw/master/docker/quickstart-load-data.yml

sed -i 's/quickstart-network/impala/' quickstart.yml
sed -i 's/quickstart-network/impala/' quickstart-kudu-minimal.yml
sed -i 's/quickstart-network/impala/' quickstart-load-data.yml

docker network create -d bridge impala
export QUICKSTART_IP=$(docker network inspect impala -f '{{(index .IPAM.Config 0).Gateway}}')
# export QUICKSTART_LISTEN_ADDR=$QUICKSTART_IP
export QUICKSTART_LISTEN_ADDR=0.0.0.0

# set QUICKSTART_LISTEN_ADDR
sed -i "s/\${QUICKSTART_LISTEN_ADDR:?Please set QUICKSTART_LISTEN_ADDR environment variable}/$QUICKSTART_LISTEN_ADDR/" quickstart.yml
sed -i "s/\${QUICKSTART_LISTEN_ADDR:?Please set QUICKSTART_LISTEN_ADDR environment variable}/$QUICKSTART_LISTEN_ADDR/" quickstart-kudu-minimal.yml
sed -i "s/\${QUICKSTART_LISTEN_ADDR:?Please set QUICKSTART_LISTEN_ADDR environment variable}/$QUICKSTART_LISTEN_ADDR/" quickstart-load-data.yml
# set QUICKSTART_IP
sed -i "s/\${QUICKSTART_IP:?Please set QUICKSTART_IP environment variable}/$QUICKSTART_IP/" quickstart-kudu-minimal.yml
sed -i "s/\${QUICKSTART_IP}/$QUICKSTART_IP/" quickstart-kudu-minimal.yml

export IMPALA_QUICKSTART_IMAGE_HASH="4.0.0"
export IMPALA_QUICKSTART_IMAGE_PREFIX="apache/impala:$IMPALA_QUICKSTART_IMAGE_HASH-"
sed -i "s/\${IMPALA_QUICKSTART_IMAGE_PREFIX:-}/apache\/impala:$IMPALA_QUICKSTART_IMAGE_HASH-/" quickstart.yml
sed -i "s/\${IMPALA_QUICKSTART_IMAGE_PREFIX:-}/apache\/impala:$IMPALA_QUICKSTART_IMAGE_HASH-/" quickstart-load-data.yml

# docker-compose -f quickstart.yml up -d
docker-compose -f quickstart.yml -f quickstart-kudu-minimal.yml -f quickstart-load-data.yml up -d
docker logs -f docker_data-loader_1
```