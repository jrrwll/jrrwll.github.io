
---
title: "MySQL SQL"
---

## auth

```sql
grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;
select user, password, host from mysql.user;
```

```sql
create user 'reader'@'%' identified by 'reader';
alter user 'reader'@'%' identified by 'reader';

grant all privileges on *.* to 'reader'@'%' identified by 'reader';
flush privileges;
grant select, insert, update, delete, create, drop, alter on scott.* to scott@localhost identified by 'tiger';
```

## date

```sql
-- today
select date_sub(curdate(), interval 0 day) ;
-- tomorrow
select date_sub(curdate(), interval -1 day) ;
-- yesterday
select date_sub(curdate(), interval 1 day) ;
-- an hour later
select date_sub(now(), interval -1 hour);
-- an hour ago
select date_sub(now(), interval 1 hour);
-- 30 minutes later
select date_add(now(),interval -30 minute)
-- 30 minutes ago
select date_add(now(),interval 30 minute)
```

```sql
-- days since 0000，float number，yesterday equals now() - 1
select to_days(now());

-- weeks
-- yearweek( date_format(  create_time, '%Y-%m-%d' ) )
-- week，weeks in a year
select yearweek(now()) ;

-- years, 20xx
-- month
-- quarter
select year( now() ) ;

-- current month
-- date_format(  create_time, '%Y%m' ) = date_format( curdate(), '%Y%m' )
-- last month
select period_diff( date_format( now(), '%Y%m' ), date_format( create_time, '%Y%m' ) );
```