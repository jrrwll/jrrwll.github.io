
---
title: "Kafka"
---

## install

```shell
docker network create mynetwork --driver bridge
mynetwork_ip=$(docker network inspect mynetwork -f '{{(index .IPAM.Config 0).Gateway}}')
docker run -d --name kafka \
--network mynetwork \
-e ALLOW_PLAINTEXT_LISTENER=yes \
-p 9092:9092 \
bitnami/kafka

# https://github.com/provectus/kafka-ui
docker run -d --name kafka-ui \
	--network mynetwork \
	-e KAFKA_CLUSTERS_0_NAME=local \
	-e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=$mynetwork_ip:9092 \
	-p 9093:8080 \
	provectuslabs/kafka-ui
```
