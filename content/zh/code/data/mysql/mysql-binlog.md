
---
title: "MySQL binlog"
---

## config

```sql
select @@session.read_buffer_size / 1024, @@global.read_buffer_size / 1024

-- 512KB for current session 
set read_buffer_size=524288
-- for global 
set @@global.read_buffer_size=524288

-- /var/run/mysqld/mysqld.sock for unix socket
show variables like 'socket';
-- /var/run/mysqld/mysqld.pid for mysql instance
show variables like 'pid_file';
```

## logs

### bin log

```sql
-- show clients
show processlist;
-- create a new binglog, just like restart
flush logs;
-- clear all binlog
reset master;

show master status;
show slave status;
show slave hosts;
show master logs;
show binary logs;

-- show binlog events in 'binlog_name' from pos limit offset, row_count;
show binlog events in "binlog.000001"; 
-- /var/lib/mysql/
show variables like 'datadir';
```

```sql
-- on/off
show variables like 'log_bin';
-- 1G per file
show variables like 'max_binlog_size';
-- 32K, cache per session to store uncommitted logs
show variables like 'binlog_cache_size';
show variables like 'Binlog_cache_use'; -- use cache
show variables like 'Binlog_cache_disk_use'; -- use tmp file when cache is too small
show variables like 'Binlog_stmt_cache_use';
show variables like 'Binlog_stmt_cache_disk_use';

-- 0/1, flush disk for every buffer write
show variables like 'sync_binlog';

-- on/off, sync binlog from master
show variables like 'log_slave_updates';

-- row, statement, mixed
-- statement, repeatable read
-- row, read committed
show variables like 'binlog_format';
```

### error log

```sql
show variables like 'log_error';
```

### slow log

```sql
-- 10 second
show variables like 'long_query_time';
-- on/off
show variables like 'log_slow_admin_statements';
show variables like 'log_slow_slave_statements';
show variables like 'log_slow_extra';
show variables like 'log_queries_not_using_indexes';
-- max logs per minute for queries_not_using_indexes, default 0 no limit
show variables like 'log_throttle_queries_not_using_indexes';

-- on/off
show variables like 'slow_query_log'; 
-- /var/lib/mysql/*-slow.log
show variables like 'slow_query_log_file';

show variables like 'slow_launch_time';
-- file/table
show variables like 'log_output';
```

```sql
set @@global.slow_query_log = on;
set global log_output = 'TABLE';

select sleep(10);
show create table mysql.slow_log;
select * from mysql.slow_log;
```

```shell
# show slow log
mysqldumpslow some-slow.log
# show 10 max lock-time records 
mysqldumpslow -s al -n 10 some-slow.log
```

### general log

```sql
-- on/off
show variables like 'general_log';
-- /var/lib/mysql/*.log
show variables like 'general_log_file';

set global general_log = 'ON';
select * from mysql.general_log;
```

## mysqlbinlog

```shell
# list my.cnf
mysql --help | grep my.cnf
mysql --help | grep 'Default options' -A 1

# show statement-format binlog
mysqlbinlog --start-position=4 binlog.000001
# show row-format binlog
mysqlbinlog -vv --start-position=4 binlog.000001

# restore
mysqlbinlog binlog.000001 | mysql -uroot -proot
mysqlbinlog binlog.000002 | mysql -uroot -proot
# import data
mysqlbinlog --start-position=4 --stop-position=106 \
	--database=test /usr/local/mysql/data/binlog.000001 | \
	mysql -uroot -proot -v test	
```
