
---
title: "Spark Install"
---

## install

### container

```shell
docker run -d -it --name spark -p 4041:4041 \
apache/spark /opt/spark/bin/spark-shell

docker run -d -it --name spark \
-p 8080:8080 -p 7077:7077 \
-e SPARK_MODE=master \
bitnami/spark
```
