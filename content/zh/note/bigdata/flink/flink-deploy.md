
---
title: "Flink Deploy"
---

## Cluster

###  Starting a Session Cluster on Docker

```shell
# https://ci.apache.org/projects/flink/flink-docs-master/docs/deployment/resource-providers/standalone/docker/#starting-a-session-cluster-on-docker

FLINK_PROPERTIES="jobmanager.rpc.address: flink-jobmanager"
docker network create flink

# launch the JobManager
docker run -d \
    --name=flink-jobmanager \
    --network flink \
    --publish 8081:8081 \
    --env FLINK_PROPERTIES="${FLINK_PROPERTIES}" \
    flink jobmanager
    
# one or more TaskManager containers    
docker run -d \
    --name=flink-taskmanager1 \
    --network flink \
    --env FLINK_PROPERTIES="${FLINK_PROPERTIES}" \
    flink taskmanager
    
# submit a task    
./bin/flink run ./examples/streaming/TopSpeedWindowing.jar    
```
