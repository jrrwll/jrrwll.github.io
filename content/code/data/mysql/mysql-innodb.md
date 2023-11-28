
---
title: "MySQL InnoDB"
---

## info

```sql
select version();
select * from information_schema.INNODB_TRX;
show engine innodb status;
```

```sql
show variables like 'innodb_version';
show variables like 'innodb_purge_threads';
show variables like 'innodb_read_io_threads';
show variables like 'innodb_write_io_threads';
-- buffer pool
show variables like 'innodb_buffer_pool_size';
show variables like 'innodb_buffer_pool_instances';

select pool_id, pool_size, free_buffers, database_pages,
       hit_rate, pages_made_young, pages_not_made_young
from information_schema.innodb_buffer_pool_stats;

-- old区域的占比, 位于midpoint之后，LRU列表的尾部部分，最近最少访问
show variables like 'innodb_old_blocks_pct';
show variables like 'innodb_old_blocks_time';

-- redo log buffer size
show variables like 'innodb_log_buffer_size';
```

## checkpoint

> 使用**LSN**, 即log sequence number

### sharp checkpoint

数据库关闭时触发，刷新所有脏页回磁盘

```sql
show variables like 'innodb_fast_shutdown';
```

### fuzzy checkpoint

- **Mater Thread Checkpoint**

每秒的循环中，当 `buf_get_modified_ratio_pct` 超过了 `innodb_max_dirty_pages_pct` ，则至多刷新100个脏页

```sql
show variables like 'innodb_max_dirty_pages_pct'; -- default 90
```
每10秒的循环中，当过去的10秒IO次数小于200，则刷新100个脏页。然后当 `buf_get_modified_ratio_pct` 超过了70%，则刷新100个脏页，否则刷新10个脏页

- **FLUSH_LRU_LIST Checkpoint**

将LRU列表尾端的数据页移出时，如果这些页有脏页，则需要刷新(Page Cleaner)

```sql
show variables like 'innodb_lru_scan_depth'; -- default 1024
```

- **Async/Sync Flush Checkpoint**

重做日志不可用时触发，又Page Cleaner Thread执行

checkpoint_age = redo_lsn - checkpoint_lsn

async_water_mark = 75% * total_redo_log_size

sync_water_mark = 90% * total_redo_log_size

当 checkpoint_age < async_water_mark时，无需刷新

当 async_water_mark < checkpoint_age < sync_water_mark时，触发Async Flush Checkpoint，从Flush列表刷新足够的脏页

当 sync_water_mark < checkpoint_age时，触发Sync Flush Checkpoint，从Flush列表刷新足够的脏页

- **Dirty Page too much Checkpoint**

脏页过多

```sh
show variables like 'innodb_max_dirty_pages_pct'; -- default 90
```

## Innodb File

### tablespace.idb

```sql
-- /disk1/ibdata1:2000M:autoextend;/disk2/ibdata2:2000M:autoextend
show variables like 'innodb_data_file_path';
-- on/off, table_name.idb
show variables like 'innodb_file_per_table';
```

### redo file

```sql
-- redo file size, ib_logfile0, ib_logfile1
show variables like 'innodb_log_file_size';
-- 2, tow files per group
show variables like 'innodb_log_files_in_group';
show variables like 'innodb_log_group_home_dir'; -- ./
```

重做日志条目：redo_log_type(1 byte) + space(<= 4 byte, tablespace id) + page_no + redo_log_body

写入重做日志时，先写入redo log buffer，再按512byte一个扇区大小进行写入，刷到重做日志文件中

## Innodb Table

表空间tablespace - 段segment - 区extent - 页page - 行row

### 表空间tablespace

独立表空间：数据，索引，插入缓冲的bitmap页

共享表空间：回滚undo信息，插入缓冲的索引页，系统事务信息，二次写缓冲double write buffer

### 段segment

数据段 leaf node segment：B+树的叶子节点 

索引段 non-leaf node segment：B+树的非叶子节点 

回滚段

### 区extent

磁盘连续页，固定1MB，默认一个区有64个连续的16KB页

### 页page

innodb_page_size 16KB，可设为8KB，4KB

数据页 B-tree Node，undo页 Undo Log Page，系统页System Page

事务数据页 Transaction System Page

插入缓冲位图页 Insert Buffer Bitmap，插入缓冲空闲列表页 Insert Buffer Free List

未压缩的二进制大对象页 Uncompressed BLOB Page，压缩的二进制大对象页 Compressed BLOB Page

### 行 row

每页 16KB / 2 - 200 = 7992行

```sql
-- Row_format: Dynamic, Compact, Redundant
show table status;
```

- **Compact**

length-list + NULL flag + head + col1 + ... + colN

变长字段长度列表：按照列的顺序逆序放置，列长大于255byte用2byte表示，否则1byte，这里varchar长度限制65536字节

NULL标志位：按照列的顺序逆序放置，该列有null值则位为1

记录头信息：5byte 40 bit，(1)  (1) (1, deleted flag 该行是否被删除) (1, min_rec_flag 预定义为最小的记录)

(4, n_owned，该记录拥有的记录数) (13, heap_no，索引堆中该记录的排序记录) 

(3, record_type 000普通，001B+树节点指针) (16, next_record，页中下一记录的相对位置)

额外的列：事务ID列 6byte，回滚指针列 7byte，未定义主键则有rowid列 6byte

### 数据页的结构

File Header + Page Header + Infimun/Supremum Records + User Record + Free Space + Page Directory + Fiel Trailer

- **File Header** 页的头信息38字节

FIL_PACE_SPACE_OR_CHKSUM 4B checksum

FIL_PAGE_OFFSET 4B 表空间中页的偏移量

FIL_PAGE_PREV 4B 当前页的上一个页，B+树叶子节点的前一个节点

FIL_PAGE_NEXT 4B 当前页的下一个页，B+树叶子节点的后一个节点

FIL_PAGE_LSN 8B 该页最后被修改的日志序列位置 Log Sequence Number

FIL_PAGE_TYPE 2B 页的类型

FIL_PAGE_FLIE_FLUSH_LSN 8B 在共享表空间才有定义，独立表空间为0

FIL_PAGE_ARCH_LOG_NO_OR_SPACE_ID 4B 表空间ID

- **Page Header  **页的状态信息 58字节

- **Infimun/Supremum Records**

Infimun Record 比当前页主键都要小的虚拟行记录，Supremum 比当前页主键都要大的虚拟行记录

- **User Record, Free Space** 

- **Page Directory**

将页装载到内存后，用于所在页的记录的二分查找

- **Fiel Trailer** 8字节

FIL_PAGE_END_LSN 8B 前四字节表示该页的checksum，后四个字节表示LSN，用于校验完整性
