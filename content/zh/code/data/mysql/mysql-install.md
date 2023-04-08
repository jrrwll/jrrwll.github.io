
---
title: "Install MySQL"
---

## install

### container

```shell
docker run -d -it --name mysql57 \
 -e MYSQL_ROOT_PASSWORD=root \
 -p 3306:3306 \
 mysql:5.7
```

## mysqladmin

```shell
# change password
mysqladmin -u myuser -p old_password password new_password

```
