
---
title: "Kafka"
---

## install

```shell
# https://github.com/provectus/kafka-ui
docker run -p 8080:8080 \
	-e KAFKA_CLUSTERS_0_NAME=local \
	-e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=kafka:9092 \
	-d provectuslabs/kafka-ui
```
