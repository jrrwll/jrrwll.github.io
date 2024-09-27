---
title: "MinIO"
---

## install
> https://min.io/docs/minio/linux/operations/install-deploy-manage/deploy-minio-single-node-multi-drive.html

```shell
docker run -d --name minio \
    -p 9000:9000 -p 9090:9090 \
    -v ./minio/config:/root/.minio \
    -v ./minio/data:/data \
    -e "MINIO_ACCESS_KEY=minioadmin" \
    -e "MINIO_SECRET_KEY=minioadmin" \
    minio/minio server /data --console-address ":9090"

# access http://:9090/
```

## client

### mc
> https://min.io/docs/minio/linux/reference/minio-mc.html

```shell
wget https://dl.min.io/client/mc/release/linux-amd64/mc
chmod +x mc
sudo mv mc /usr/local/bin/mc

brew install minio/stable/mc
```

```shell
# mc alias set <ALIAS> <YOUR-MINIO-ENDPOINT> [YOUR-ACCESS-KEY] [YOUR-SECRET-KEY]
mc alias set dev http://192.168.1.x:9000 minioadmin minioadmin

mc admin info dev
# create bucket
mc mb dev/test
mc ls dev
```
