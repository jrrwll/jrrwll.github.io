
---
title: "MySQL Conf"
---

## info

```sql
select variables like "%version%";
```

### system databases

```sql
show engines;
select * from information_schema.engines;
```

## conf

### auth

```sql
select user();
select host, user, ssl_cipher, x509_issuer,x509_subject from mysql.user;
```

#### change password

```sql
-- after login without password via: mysql -u root -p
set password = password('root');
set password for root@localhost = password('new-password'); 

use mysql;
update user set authentication_string=password("new-password") where user="root";
update user set password=password('new-password') where user='root' and host='localhost'; 
flush privileges;
```

#### forget root password

```shell
mysqld --skip-grant-tables
# in another terminal tab 
mysql -u root -p
```

```conf
# empty password to login
[mysqld]
skip-grant-tables
```

### time zone

```sql
show variables like '%time_zone%'
-- jdbc:mysql://xxx?serverTimezone=Asia/Shanghai
-- jdbc:mysql://xxx?serverTimezone=UTC
select now();
select @@global.time_zone;
select @@session.time_zone;
```

```shell
rm -rf /etc/localtime
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

sed -i '/^\[mysqld\]/a default-time-zone = "+08:00"' /etc/mysql/my.cnf 
```

### character

```sql
-- utf8mb4
show variables like '%character%';
```

### socket

```sql
show variables like 'socket';
```

```shell
# use Unix socket to connect to MySQL server
# -S socket file to use for connection.
mysql -uroot -proot -S /var/run/mysqld/mysqld.sock
```
