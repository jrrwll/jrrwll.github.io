
---
title: "StarRocks Java UDF"
---

## Implements

< https://docs.starrocks.io/zh/docs/sql-reference/sql-functions/JAVA_UDF


- Scalar UDF 标量函数

可以对单行数据进行操作，输出单行结果

```java
// evaluate 方法为 UDF 调用入口，必须是 public 成员方法。
R evaluate(T1 param1, ...)	
```

- User Defined Aggregation Function 聚合函数

对多行数据进行操作，输出单行结果

```java
// 创建 State。State是自定义数据类，需要实现 int serializeLength() 方法来指定中间结果的序列化长度(单位为 Byte)
State create();

// 销毁 State
void destroy(State state);

// 更新 State 。其中第一个参数是 State，其余的参数是函数声明的输入参数
void update(State state, T1 param1, ...);

// 序列化 State，保存到 java.nio.ByteBuffer
void serialize(State state, ByteBuffer buffer);

// 将ByteBuffer中缓存的数据合并到State
void merge(State state, ByteBuffer buffer);

// 通过 State 获取函数的最终结果
R finalize(State state);
```

- User Defined Window Function 窗口函数

窗口函数针对一组行（一个窗口）计算值，并为每行返回一个结果。一般情况下，窗口函数包含 OVER 子句，将数据行拆分成多个分组，窗口函数基于每一行数据所在的组（一个窗口）进行计算，并为每行返回一个结果

**分区**：OVER子句中 PARTITION BY 指定分区列， 分区列的值相同的行被视为在同一个分区内

**窗口框架**：window frame 子句指定了运算范围，以当前行为准，前后若干行作为窗口函数运算的对象。例如 ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING，表示运算范围为当前行和它前后各一行数据

```java
// 重置窗口，用于清空State
void reset(State state);

// peer_group_start 当前分区开始的位置
// peer_group_end 当前分区结束的位置
// frame_start 当前窗口框架（window frame）起始位置
// frame_end 当前窗口框架（window frame）结束位置
// inputs 表示一个窗口中输入的数据，为包装类数组。包装类需要对应输入数据的类型
void windowUpdate(State state, 
        int peer_group_start, int peer_group_end,
        int frame_start, int frame_end, T[] inputs);
```

- User Defined Table Function 表格函数

读入一行数据，输出多个值可视为一张表。表格函数常用于实现行转列

```java
// process() 方法为 UDTF 调用入口，需要返回数组。
R[] process(T1 param1, ...);
```

## Config

**fe.conf** `enable_udf = true`

```sql
-- show full builtin functions;
show full global functions;

-- 创建 Scalar UDF
create global function MY_UDF_JSON_GET(string, string) returns string
properties (
    "symbol" = "com.starrocks.udf.sample.UDFJsonGet", 
    "type" = "StarrocksJar",
    "file" = "http://http_host:http_port/udf-1.0-SNAPSHOT-jar-with-dependencies.jar"
);

-- 创建 UDAF
create global aggregate function MY_SUM_INT(int) returns int
properties (
    "symbol" = "com.starrocks.udf.sample.SumInt", 
    "type" = "StarrocksJar",
    "file" = "http://http_host:http_port/udf-1.0-SNAPSHOT-jar-with-dependencies.jar"
);

-- UDWF
create global aggregate function MY_WINDOW_SUM_INT(int) returns int
properties (
    -- 所创建的函数是否为窗口函数
    "analytic" = "true",
    "symbol" = "com.starrocks.udf.sample.WindowSumInt", 
    "type" = "StarrocksJar",
    "file" = "http://http_host:http_port/udf-1.0-SNAPSHOT-jar-with-dependencies.jar"
);

-- 创建 UDTF
create global table function MY_UDF_SPLIT(string) returns string
properties (
    "symbol" = "com.starrocks.udf.sample.UDFSplit", 
    "type" = "StarrocksJar",
    "file" = "http://http_host:http_port/udf-1.0-SNAPSHOT-jar-with-dependencies.jar"
);

drop global function MY_UDF_SPLIT(string);
```

**how to use it**

```sql
with t1 as (
    select 1 as a, 2.1 as b, 'hello world' as c
    union all
    select 2 as a, 2.2 as b, 'hello UDTF' as c 
)
select t1.a, t1.b, MY_UDF_SPLIT
from t1, MY_UDF_SPLIT(t1.c);
```
