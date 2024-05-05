
---
title: "StarRocks Install"
---

## docker

```shell
docker run -itd --name sr \
    -p 9030:9030 -p 8030:8030 -p 8040:8040 \
    starrocks/allin1-ubuntu

docker exec -it sr \
    mysql -P 9030 -h 127.0.0.1 -u root --prompt="StarRocks > "
```
