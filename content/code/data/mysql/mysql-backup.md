
---
title: "Dump/Store for MySQL"
---

## intro

### 备份的类型

- 方法

热备hot/online：数据库运行中直接备份，对正在运行的数据库操作没有任何影响
冷备cold/offline：数据库停止时的备份
温备warm：数据库运行中直接备份，但是对数据库有影响，加全局读锁

- 备份文件类型

逻辑备份：文本文件
裸文件备份：二进制文件

- 备份的内容

完全备份：完整的备份
增量备份：上次完全备份的基础上，对于更改的数据进行备份
日志备份：binlog备份

#### 冷备

配置文件 `my.cnf`，
共享表空间文件 `ibdata1`
独立表空间文件 `*.ibd`
重做日志文件 `ib_logfile0`
回滚日志文件 `undo_001`

## mysqldump

```shell
# --all-databases
# --databases mydb1 mydb2
# --single-transaction 备份前，先执行start transaction 在一个事务中获取一致性
# --hex-blob 将binary，varbinary，blob，bit列备份为十六进制格式
# -d --no-data 仅元数据
mysqldump --host=host1 -uroot -proot \
--skip-lock-tables --skip-add-locks \
--no-data --databases mydb1 > test_backup.sql

mysql -uroot -proot -h127.0.0.1 -P3306 < test_backup.sql
```

```sql
create database mydb2;
use mydb2;
source test_backup.sql
```

```shell
mysqldump -uroot -proot mydb test_table > test_table.sql
```

## load data

```sql
-- 导出一张表的某些列到文件
select col1, col2
into outfile 'test_backup.csv'
fields terminated by '\t'
enclosed by ''
escaped by '\\'
lines terminated by '\n'
starting by ''
from test_table where 1=1;
```

```sql
-- show variables like "secure_file_priv";
load data infile '/var/lib/mysql-files/file.csv'
into table table_name
fields terminated by ',' enbclosed by '"'
lines terminated by '\n'
ignore 1 rows;

-- 导入到指定表
-- 其中col1, col2为文件中的数据，col3为计算列
load data infile 'test_backup.csv'
into table test_table
fields terminated by ',' ignore 1 lines
(col1, col2) set col3 = col1 + col2;
```

## mysqlimport

```shell
mysqlimport --use-treads=2 a a1.csv a2.csv 
```

## mysqlbinlog

```shell
mysqlbinlog --start-position=4 --stop-position=1000 binlog.[0-9]* | mysql -uroot -proot

mysqlbinlog binlog.0001 > statements.sql
```

## xtrabackup

```shell
brew install percona-xtrabackup

# 完全热备份
xtrabackup -H 127.0.0.1 -uroot -proot --datadir=. --target-dir=$HOME/data/mysql/backup --backup 
```
