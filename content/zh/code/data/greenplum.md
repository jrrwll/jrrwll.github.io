
---
title: "GreenPlum"
---

## install

```shell
# root/pivotal gpadmin/pivotal
docker run -it -d --name greenplum \
    -e GP_USER=myuser \
    -e GP_PASSWORD=mypassword \
    -e GP_DB=testdb \
    -p 5432:5432 \
    --privileged=true \
    datagrip/greenplum:6.8

su - gpadmin
psql
```
