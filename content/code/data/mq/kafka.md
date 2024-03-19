
---
title: "Kafka"
---

## install

```shell
sudo chown 1001 $PWD/kafka
# https://developer.confluent.io/learn/kraft/ instead of zookeeper  
docker run -d --hostname kafka --name kafka \
	-e KAFKA_CFG_NODE_ID=0 \
    -e KAFKA_CFG_PROCESS_ROLES=controller,broker \
    -e KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093 \
    -e KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT \
    -e KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka:9093 \
    -e KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER \
	-v $PWD/kafka:/bitnami/kafka \
	-p 9092:9092 \
	bitnami/kafka

# https://github.com/provectus/kafka-ui
docker run -d --name kafka-ui \
	--link kafka \
	-e KAFKA_CLUSTERS_0_NAME=local \
	-e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=kafka:9092 \
	-p 9093:8080 \
	provectuslabs/kafka-ui
```

```shell
# list topics
docker run -it --rm \
    --link kafka \
    bitnami/kafka kafka-topics.sh --list  --bootstrap-server kafka:9092
```
