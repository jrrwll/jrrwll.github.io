
---
title: "Dump/Store for MySQL"
---

## mysqldump

```shell
# my_db2 需要提前创建
mysqldump --host=host1 -uroot -proot \
--skip-lock-tables --skip-add-locks -C \
--no-data --databases my_db1 > tmp.sql | mysql -uroot -proot my_db2

mysqldump -uroot -proot \
--skip-lock-tables --skip-add-locks \
--no-data --databases my_db1 > tmp.sql
```

```sql
create database my_db2;
use my_db2;
source tmp.sql
```
