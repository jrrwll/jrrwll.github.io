
---
title: "Microsoft SQL Server"
---

## install

### container

```shell
docker run -d -it --name sqlserver \
    -e "ACCEPT_EULA=Y" \
    -e "MSSQL_SA_PASSWORD=Your_Strong_Passwd" \
    -p 1433:1433 \
    mcr.microsoft.com/mssql/server:2019-CU14-ubuntu-20.04
```
