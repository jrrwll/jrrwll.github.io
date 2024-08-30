
---
title: "Audit for MySQL"
---

## process

```sql
show processlist;

select * from information_schema.processlist
where command != 'Sleep';
select * from information_schema.processlist
where host like '192.168.1.%';

-- 杀掉空闲时间在600秒以上的链接，拼接得到kill语句
select concat('kill ',id,';') from information_schema.processlist
where command = 'Sleep' and time > 600 order by time desc;

-- 杀掉某个用户发起的链接，拼接得到kill语句
select concat('kill ',id,';') from information_schema.processlist
where user = 'some_user' order by time desc;
```

