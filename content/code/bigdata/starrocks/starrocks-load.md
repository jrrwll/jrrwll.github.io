---
title: "StarRocks Load"
---

## Flink
> https://docs.starrocks.io/zh/docs/loading/Flink-connector-starrocks/

```xml
<dependency>
    <groupId>com.starrocks</groupId>
    <artifactId>flink-connector-starrocks</artifactId>
    <version>${connector_version}_flink-${flink_version}</version>
</dependency>
```

```sql
CREATE TABLE `score_board` (
    `id` INT,
    `name` STRING,
    `score` INT,
    PRIMARY KEY (id) NOT ENFORCED
) WITH (
    'connector' = 'starrocks',
    'jdbc-url' = 'jdbc:mysql://127.0.0.1:9030',
    'load-url' = '127.0.0.1:8030',
    'database-name' = 'test',
    'table-name' = 'score_board',
    'username' = 'root',
    'password' = '',
-- 'sink.properties.partial_update' = 'true',
);
```
