
---
title: "MySQL Transaction"
---

## 锁 lock

### 一致性非锁定读

通过行多版本控制，如果读取的行正在delete或update，则读取行的undo段的快照数据

read committed 事务隔离级别下，读取被锁定行的最新快照

repeatable read 事务隔离级别下(默认级别)，读取本事务开始时的该行的快照

| 时间 | 会话A                            | 会话B                                |
| ---- | -------------------------------- | ------------------------------------ |
| 1    | `begin`                          |                                      |
| 2    | `select * from tmp where id = 1` |                                      |
| 3    |                                  | `begin`                              |
| 4    |                                  | `update tmp set id = 2 where id = 1` |
| 5    | `select * from tmp where id = 1` |                                      |
| 6    |                                  | `commit`                             |
| 7    | `select * from tmp where id = 1` |                                      |
| 8    | `commit`                         |                                      |

### 一致性锁定读

```sql
-- X lock
select * from tmp where id = 1 for update;
-- S lock
select * from tmp where id = 1 lock in share mode;
```

### 自增长锁 

```sql
-- 0 使用表锁AUTO-INC Locking，针对每条插入SQL加锁，执行完立刻释放
-- 1 对simple inserts(插入前可以确定插入行数)用互斥量mutex累加内存中的计数器，对bulk inserts使用表锁
-- 2 都使用互斥量mutex，增长可能不连续，需使用row binlog
show variables like 'innodb_autoinc_lock_mode'; -- default is 2
```

### 行锁的实现

- **Record Lock** 单行锁，锁定单行记录(会锁住索引记录)
- **Gap Lock** 间隙锁，锁定一个范围，但不包含记录本身
- **Next-Key Lock** 单行锁 + 间隙锁

**Next-Key Locking**：对于索引 a, b, c, d，可能被锁住的区间为**(-oo, a]，(a, b]，(b, c]，(c, d]，(d, +oo)**

**Previous-Key Locking**：对于索引 a, b, c, d，可能被锁住的区间为**(-oo, a)，[a, b)，[b, c)，[c, d)，[d, +oo)**

当使用 `select xxx for update` 锁住主键索引时，**Next-Key Lock**降级为**Record Lock**

```sql
create table test_next_key_lock(a int , b int, primary key (a), key(b));
insert into test_next_key_lock
values (1, 1), (3, 1), (5, 3), (7, 6), (10, 8);

-- 事务A
start transaction;
-- record lock on a=5
-- gap lock on b in (1, 3) and (3, 6)
select * from test_next_key_lock where b = 3 for update;

-- 事务B
start transaction;
-- Lock wait timeout exceeded; try restarting transaction
insert into test_next_key_lock values (9, 3);
-- blocked since record 'a = 5' has a X Lock
select * from test_next_key_lock where a =5 lock in share mode;
-- blocked since record 'b = 2' in (1, 3)
insert into test_next_key_lock values (4, 2);
-- blocked since record 'b = 2' in (3, 6)
insert into test_next_key_lock values (6, 5);
```

```sql
-- 关闭 Gap Lock，会丢失可重复读的隔离性
set transaction isolation level read committed;
```

## 隔离性 isolation

```sql
select @@transaction_isolation;
select @@global.transaction_isolation;
set session transaction isolation level read committed;
```

```conf
[mysqld]
transaction-isolation=read-committed
```

- ### read uncommitted

脏读，可能读到未提交的数据

- ### read committed

幻读，可能读到其他事务已提交的数据，其中该事务提交发生在本事务开始之后

- ### repeatable read

使用next-key locking 避免幻读

- ### serializable

为每个读操作加S Lock，即 `lock in share mode`，不支持一致性非锁定读

## 重做日志 redo log

事务修改的数据

从 **redo log buffer** 写入 **redo log file**，需要 **fsync**

```sql
-- 0，不刷会，交由Master Thread每秒主动刷回
-- 1，每次事务提交都需要fsync刷回文件
-- 2，每次事务提交仅写会文件系统缓存中
show variables like 'innodb_flush_log_at_trx_commit'; -- 1
```

## 回滚日志 undo log

行记录的版本

对每个**insert**，对应一个**delete**

对每个**delete**，对应一个**insert**

对每个**update**，对应一个相反的**update**
