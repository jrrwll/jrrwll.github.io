
---
title: "Hudi Install"
---

## Docker

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

### Step 1 : Publish the first batch to Kafka
```shell
# brew install kcat
sudo apt-get install kafkacat jq -y 
cat demo/data/batch_1.json | kafkacat -b kafkabroker -t stock_ticks -P
kafkacat -b kafkabroker -L -J | jq 
```

### Step 2: Incrementally ingest data from Kafka topic
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

### Step 3: Sync with Hive

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

### Step 4 (a): Run Hive Queries

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

### Step 4 (b): Run Spark-SQL Queries

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

### Step 4 (c): Run Presto/Trino Queries

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

# Incrementally ingest data from Kafka topic
docker exec -it adhoc-2 /bin/bash

# http://namenode:50070/explorer.html#/user/hive/warehouse/stock_ticks_cow/2018/08/31
# http://namenode:50070/explorer.html#/user/hive/warehouse/stock_ticks_mor/2018/08/31
```

### Step 6 (a): Incremental Query for COPY-ON-WRITE Table

```sql
-- beeline to hive

select `_hoodie_commit_time`, symbol, ts, volume, open, close from stock_ticks_cow where symbol = 'GOOG';

set hoodie.stock_ticks_cow.consume.mode=INCREMENTAL;
set hoodie.stock_ticks_cow.consume.max.commits=3;
-- 将开始时间设置为提交时间（20180924064621）
set hoodie.stock_ticks_cow.consume.start.timestamp=20180924064621;

select `_hoodie_commit_time`, symbol, ts, volume, open, close from stock_ticks_cow where symbol = 'GOOG' and `_hoodie_commit_time` > '20180924064621';
```

### Step 6 (b): Incremental Query with Spark SQL

```ruby
# $SPARK_INSTALL/bin/spark-shell

import org.apache.hudi.DataSourceReadOptions

val hoodieIncViewDF = spark.read.format("org.apache.hudi").option(DataSourceReadOptions.QUERY_TYPE_OPT_KEY, DataSourceReadOptions.QUERY_TYPE_INCREMENTAL_OPT_VAL).option(DataSourceReadOptions.BEGIN_INSTANTTIME_OPT_KEY, "20180924064621").load("/user/hive/warehouse/stock_ticks_cow")
hoodieIncViewDF.registerTempTable("stock_ticks_cow_incr_tmp1")

spark.sql("select `_hoodie_commit_time`, symbol, ts, volume, open, close from stock_ticks_cow_incr_tmp1 where symbol = 'GOOG'").show(100, false);
```

### Step 7: Schedule and Run Compaction for Merge-On-Read table

```shell
docker exec -it adhoc-1 /bin/bash
/var/hoodie/ws/hudi-cli/hudi-cli.sh
```

```ruby
connect --path /user/hive/warehouse/stock_ticks_mor
compactions show all
# 使用Spark Launcher压缩
compaction schedule --hoodieConfigs hoodie.compact.inline.max.delta.commits=1
refresh
compactions show all

compaction run --compactionInstant 20180924070031 --parallelism 2 --sparkMemory 1G --schemaFilePath /var/demo/config/schema.avsc --retry 1
refresh
compactions show all
```

### Step 8: Run Hive Queries including incremental queries

```sql
-- Read Optimized Query
select symbol, max(ts) from stock_ticks_mor_ro group by symbol HAVING symbol = 'GOOG';
select `_hoodie_commit_time`, symbol, ts, volume, open, close from stock_ticks_mor_ro where symbol = 'GOOG';
-- Snapshot Query
select symbol, max(ts) from stock_ticks_mor_rt group by symbol HAVING symbol = 'GOOG';
select `_hoodie_commit_time`, symbol, ts, volume, open, close from stock_ticks_mor_rt where symbol = 'GOOG';
```

```sql
-- Incremental Query
set hoodie.stock_ticks_mor.consume.mode=INCREMENTAL;
set hoodie.stock_ticks_mor.consume.max.commits=3;
set hoodie.stock_ticks_mor.consume.start.timestamp=20180924064636;

select `_hoodie_commit_time`, symbol, ts, volume, open, close from stock_ticks_mor_ro where symbol = 'GOOG' and `_hoodie_commit_time` > '20180924064636';
```

### Step 9: Read Optimized and Snapshot queries for MOR with Spark-SQL after compaction

```ruby
# Read Optimized Query
spark.sql("select symbol, max(ts) from stock_ticks_mor_ro group by symbol HAVING symbol = 'GOOG'").show(100, false)
spark.sql("select `_hoodie_commit_time`, symbol, ts, volume, open, close from stock_ticks_mor_ro where symbol = 'GOOG'").show(100, false)
# Snapshot Query
spark.sql("select symbol, max(ts) from stock_ticks_mor_rt group by symbol HAVING symbol = 'GOOG'").show(100, false)
spark.sql("select `_hoodie_commit_time`, symbol, ts, volume, open, close from stock_ticks_mor_rt where symbol = 'GOOG'").show(100, false)
```

### Step 10: Presto Read Optimized queries on MOR table after compaction

```shell
docker exec -it presto-worker-1 presto --server presto-coordinator-1:8090
```

```sql
use hive.default;
-- Read Optimized Query
select symbol, max(ts) from stock_ticks_mor_ro group by symbol HAVING symbol = 'GOOG';
select "_hoodie_commit_time", symbol, ts, volume, open, close from stock_ticks_mor_ro where symbol = 'GOOG';
```
