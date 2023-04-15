
---
title: "Hive Install"
---

## docker
> https://github.com/big-data-europe/docker-hive

```shell
git clone https://github.com/big-data-europe/docker-hive -d
cd docker-hive
docker-compose up -d
```

**testing**

```shell
docker-compose exec hive-server bash
/opt/hive/bin/beeline -u jdbc:hive2://localhost:10000

# https://prestosql.io/docs/current/installation/cli.html
wget https://repo1.maven.org/maven2/io/prestosql/presto-cli/308/presto-cli-308-executable.jar
mv presto-cli-308-executable.jar presto
chmod +x presto
./presto --server localhost:8080 --catalog hive --schema default
```

```sql
CREATE TABLE pokes (foo INT, bar STRING);
LOAD DATA LOCAL INPATH '/opt/hive/examples/files/kv1.txt' OVERWRITE INTO TABLE pokes;

select * from pokes;
```
