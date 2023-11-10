
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

## mr3
> https://mr3docs.datamonad.com/docs/quick/k8s/configure-kerberos/

```shell
# root:passwd for MySQL
# 6080 admin:rangeradmin1 for Ranger
# 6083 solr:solrRock for Solr
# 9852 HiveServer2
# 8188 Timeline Server
docker run -itd --privileged --name mr3 \
-v /sys/fs/cgroup:/sys/fs/cgroup:ro \
-p 6080:6080 -p 8188:8188 -p 9852:9852 \
mr3project/hivemr3-all:1.2

su hive
# /opt/mr3-run
ls -lah /opt/mr3-run/work-dir/warehouse/
hive/run-beeline.sh
```

```sql
-- wget https://github.com/mr3project/mr3-release/releases/download/v1.0/pokemon.csv
CREATE TABLE pokemon (Number Int,Name String,Type1 String,Type2 String,Total Int,HP Int,Attack Int,Defense Int,Sp_Atk Int,Sp_Def Int,Speed Int) row format delimited fields terminated BY ',' lines terminated BY '\n' tblproperties("skip.header.line.count"="1");
load data local inpath '/opt/mr3-run/pokemon.csv' INTO table pokemon;

select avg(HP) from pokemon;
create table pokemon1 as select *, IF(HP>160.0,'strong',IF(HP>140.0,'moderate','weak')) AS power_rate from pokemon;
select COUNT(name), power_rate from pokemon1 group by power_rate;
```

**mr3-ui**

```shell
wget https://github.com/mr3project/mr3-release/releases/download/v1.0/mr3-ui-1.0.tar.gz
tar xvf mr3-ui-1.0.tar.gz
cd mr3-ui-0.8.5/
# hosts.timeline: "your.server.address:8188"
config/configs.env
# http://your.webserver.address/mr3-ui-0.8.5/
```

**systemctl**

```shell
docker run mr3project/hivemr3-all:1.2 /usr/sbin/init
# env.sh
# conf/mr3-site.xml
# conf/hive-site.xml
./run-all.sh
```
