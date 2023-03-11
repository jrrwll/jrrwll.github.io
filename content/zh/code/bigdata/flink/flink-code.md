
---
title: "Flink Code"
---

## `flink-client`

### CliFrontend

> build StreamGraph
>
> org.apache.flink.client.cli.CliFrontend#main

```shell
FLINK_CONF_DIR=./flink-dist/src/main/resources

# submit a job
run ./flink-examples/flink-examples-streaming/target/WordCount.jar
```

## `flink-runtime`

### StandaloneSessionClusterEntrypoint

> run JobManager
>
> org.apache.flink.runtime.entrypoint.StandaloneSessionClusterEntrypoint

```shell
-c ./flink-dist/src/main/resources
```

