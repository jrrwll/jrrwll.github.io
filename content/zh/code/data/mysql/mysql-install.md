
---
title: "Install MySQL"
---

## Container

```shell
docker run -d -it --name mysql57 \
 -e MYSQL_ROOT_PASSWORD=root \
 -p 3306:3306 \
 mysql:5.7
```
