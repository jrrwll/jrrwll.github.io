
---
title: "Hudi"
---

## 概念

### 表类型 Table Type

- **写时复制 Copy On Write**

支持 Snapshot Queries 快照查询、Incremental Queries 增量查询

仅使用列式文件格式（例如 parquet）存储数据。通过在写入期间执行同步合并来简单更新版本并重写文件

- **读时合并 Merge On Read**

支持快照查询、增量查询、Read Optimized Queries 读取优化查询

使用列式（例如 parquet）+基于行（例如 avro）文件格式的组合来存储数据。更新会记录到增量文件中，然后进行压缩，以同步或异步方式生成新版本的列式文件

下表总结了这两种表类型之间的权衡

| 权衡 Trade-off             | CopyOnWrite                          | MergeOnRead                                                  |
| -------------------------- | ------------------------------------ | ------------------------------------------------------------ |
| 数据延迟 Data Latency      | 更高 Higher                          | 更低 Lower                                                   |
| 查询延迟 Query Latency     | 更低 Lower                           | 更高 Higher                                                  |
| 更新成本 Update cost (I/O) | 更高，需要重写整个parquet            | 更低，追加到增量日志                                         |
| Parquet文件大小            | 更小 Smaller (high update(I/0) cost) | 更大 Larger (low update cost)                                |
| 写放大 Write Amplification | Higher                               | 较低且取决于压缩策略 Lower (depending on compaction strategy) |

### 查询类型 Query types

- **快照查询 Snapshot Queries**

查询会看到给定提交或压缩操作时表的最新快照。在读取表上合并的情况下，它通过即时合并最新文件切片的基本文件和增量文件来公开近乎实时的数据（几分钟）。对于写时复制表，它提供了现有parquet表的直接替代品，同时提供更新插入/删除和其他写入侧功能。

- **增量查询 Incremental Queries**

查询只能看到自给定提交/压缩以来写入表的新数据。这有效地提供了变更流以启用增量数据管道

- **读取优化查询 Read Optimized Queries**

查询会看到给定提交/压缩操作时表的最新快照。仅公开最新文件切片中的基础/列式文件，并保证与非 hudi 列式表相同的列式查询性能

## Install

### Docker

```shell
git clone https://kgithub.com/apache/hudi --depth 1
cd hudi
mvn clean package -Pintegration-tests -DskipTests -Dspark2.4 -Dscala-2.11

cat <<EOF >> /etc/hosts
127.0.0.1 adhoc-1 adhoc-2
127.0.0.1 namenode datanode1
127.0.0.1 hiveserver hivemetastore
127.0.0.1 kafkabroker
127.0.0.1 sparkmaster
127.0.0.1 zookeeper
EOF

cd docker
# ./setup_demo.sh --mac-aarch64
./setup_demo.sh
docker ps
```

#### Step 1 : Publish the first batch to Kafka
```shell
# brew install kcat
sudo apt-get install kafkacat jq -y
kafkacat 
cat demo/data/batch_1.json | kafkacat -b kafkabroker -t stock_ticks -P
kafkacat -b kafkabroker -L -J | jq 
```

#### Step 2: Incrementally ingest data from Kafka topic
```shell
docker exec -it adhoc-2 /bin/bash

# ingest to stock_ticks_cow table in HDFS
spark-submit \
  --class org.apache.hudi.utilities.deltastreamer.HoodieDeltaStreamer $HUDI_UTILITIES_BUNDLE \
  --table-type COPY_ON_WRITE \
  --source-class org.apache.hudi.utilities.sources.JsonKafkaSource \
  --source-ordering-field ts  \
  --target-base-path /user/hive/warehouse/stock_ticks_cow \
  --target-table stock_ticks_cow --props /var/demo/config/kafka-source.properties \
  --schemaprovider-class org.apache.hudi.utilities.schema.FilebasedSchemaProvider

# ingest to stock_ticks_mor table in HDFS
spark-submit \
  --class org.apache.hudi.utilities.deltastreamer.HoodieDeltaStreamer $HUDI_UTILITIES_BUNDLE \
  --table-type MERGE_ON_READ \
  --source-class org.apache.hudi.utilities.sources.JsonKafkaSource \
  --source-ordering-field ts \
  --target-base-path /user/hive/warehouse/stock_ticks_mor \
  --target-table stock_ticks_mor \
  --props /var/demo/config/kafka-source.properties \
  --schemaprovider-class org.apache.hudi.utilities.schema.FilebasedSchemaProvider \
  --disable-compaction

# use HDFS web-browser to look at the tables
# http://namenode:50070/explorer.html#/user/hive/warehouse/stock_ticks_cow
# http://namenode:50070/explorer.html#/user/hive/warehouse/stock_ticks_mor
```

#### Step 3: Sync with Hive

```shell
docker exec -it adhoc-2 /bin/bash
# sync the HDFS state to Hive
/var/hoodie/ws/hudi-sync/hudi-hive-sync/run_sync_tool.sh \
  --jdbc-url jdbc:hive2://hiveserver:10000 \
  --user hive \
  --pass hive \
  --partitioned-by dt \
  --base-path /user/hive/warehouse/stock_ticks_cow \
  --database default \
  --table stock_ticks_cow \
  --partition-value-extractor org.apache.hudi.hive.SlashEncodedDayPartitionValueExtractor

/var/hoodie/ws/hudi-sync/hudi-hive-sync/run_sync_tool.sh \
  --jdbc-url jdbc:hive2://hiveserver:10000 \
  --user hive \
  --pass hive \
  --partitioned-by dt \
  --base-path /user/hive/warehouse/stock_ticks_mor \
  --database default \
  --table stock_ticks_mor \
  --partition-value-extractor org.apache.hudi.hive.SlashEncodedDayPartitionValueExtractor
```

#### Step 4 (a): Run Hive Queries

```shell
docker exec -it adhoc-2 /bin/bash
beeline -u jdbc:hive2://hiveserver:10000 \
  --hiveconf hive.input.format=org.apache.hadoop.hive.ql.io.HiveInputFormat \
  --hiveconf hive.stats.autogather=false
```

```sql
show tables;
show partitions stock_ticks_mor_rt;

select symbol, max(ts) from stock_ticks_cow group by symbol HAVING symbol = 'GOOG';
select `_hoodie_commit_time`, symbol, ts, volume, open, close  from stock_ticks_cow where  symbol = 'GOOG';

select symbol, max(ts) from stock_ticks_mor_ro group by symbol HAVING symbol = 'GOOG';
select symbol, max(ts) from stock_ticks_mor_rt group by symbol HAVING symbol = 'GOOG';
select `_hoodie_commit_time`, symbol, ts, volume, open, close  from stock_ticks_mor_ro where  symbol = 'GOOG';
select `_hoodie_commit_time`, symbol, ts, volume, open, close  from stock_ticks_mor_rt where  symbol = 'GOOG';
```

#### Step 4 (b): Run Spark-SQL Queries

```shell
docker exec -it adhoc-1 /bin/bash
$SPARK_INSTALL/bin/spark-shell \
  --jars $HUDI_SPARK_BUNDLE \
  --master local[2] \
  --driver-class-path $HADOOP_CONF_DIR \
  --conf spark.sql.hive.convertMetastoreParquet=false \
  --deploy-mode client \
  --driver-memory 1G \
  --executor-memory 3G \
  --num-executors 1
```

```sql
spark.sql("show tables").show(100, false)
spark.sql("select symbol, max(ts) from stock_ticks_cow group by symbol HAVING symbol = 'GOOG'").show(100, false)
spark.sql("select `_hoodie_commit_time`, symbol, ts, volume, open, close  from stock_ticks_cow where  symbol = 'GOOG'").show(100, false)

spark.sql("select symbol, max(ts) from stock_ticks_mor_ro group by symbol HAVING symbol = 'GOOG'").show(100, false)
spark.sql("select symbol, max(ts) from stock_ticks_mor_rt group by symbol HAVING symbol = 'GOOG'").show(100, false)
spark.sql("select `_hoodie_commit_time`, symbol, ts, volume, open, close  from stock_ticks_mor_ro where  symbol = 'GOOG'").show(100, false)
spark.sql("select `_hoodie_commit_time`, symbol, ts, volume, open, close  from stock_ticks_mor_rt where  symbol = 'GOOG'").show(100, false)
```

#### Step 4 (c): Run Presto/Trino Queries

```shell
docker exec -it presto-worker-1 presto --server presto-coordinator-1:8090

docker exec -it adhoc-2 trino --server trino-coordinator-1:8091
```

```sql
show catalogs;
use hive.default;
show tables;

select symbol, max(ts) from stock_ticks_cow group by symbol HAVING symbol = 'GOOG';
select "_hoodie_commit_time", symbol, ts, volume, open, close from stock_ticks_cow where symbol = 'GOOG';
select symbol, max(ts) from stock_ticks_mor_ro group by symbol HAVING symbol = 'GOOG';
select "_hoodie_commit_time", symbol, ts, volume, open, close  from stock_ticks_mor_ro where  symbol = 'GOOG';
exit
```

### Step 5: Upload second batch to Kafka and run DeltaStreamer to ingest

```shell
cat demo/data/batch_2.json | kafkacat -b kafkabroker -t stock_ticks -P

docker exec -it adhoc-2 /bin/bash
```

```shell
spark-submit \
  --class org.apache.hudi.utilities.deltastreamer.HoodieDeltaStreamer $HUDI_UTILITIES_BUNDLE \
  --table-type COPY_ON_WRITE \
  --source-class org.apache.hudi.utilities.sources.JsonKafkaSource \
  --source-ordering-field ts \
  --target-base-path /user/hive/warehouse/stock_ticks_cow \
  --target-table stock_ticks_cow \
  --props /var/demo/config/kafka-source.properties \
  --schemaprovider-class org.apache.hudi.utilities.schema.FilebasedSchemaProvider
spark-submit \
  --class org.apache.hudi.utilities.deltastreamer.HoodieDeltaStreamer $HUDI_UTILITIES_BUNDLE \
  --table-type MERGE_ON_READ \
  --source-class org.apache.hudi.utilities.sources.JsonKafkaSource \
  --source-ordering-field ts \
  --target-base-path /user/hive/warehouse/stock_ticks_mor \
  --target-table stock_ticks_mor \
  --props /var/demo/config/kafka-source.properties \
  --schemaprovider-class org.apache.hudi.utilities.schema.FilebasedSchemaProvider \
  --disable-compaction

# http://namenode:50070/explorer.html#/user/hive/warehouse/stock_ticks_cow/2018/08/31
# http://namenode:50070/explorer.html#/user/hive/warehouse/stock_ticks_mor/2018/08/31
```

### Step 7 (a): Incremental Query for COPY-ON-WRITE Table
