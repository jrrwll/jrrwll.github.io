
---
title: "Docker Inspect"
---

## inspect

```shell
# ip
# docker network create mynetwork --driver bridge
docker network inspect mynetwork -f '{{(index .IPAM.Config 0).Gateway}}'
```

## ps

```shell
# get cid by cname
docker ps -aqf "name=mysql57"
```
