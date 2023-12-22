
---
title: "Gradle"
---

## install

https://mirrors.cloud.tencent.com/gradle/gradle-8.4-all.zip

```shell

```

## build

```shell
# -x skip task
gradle build -x tets -x checkstyleMain
```

## performance

```shell
gradle classes --parallel
```

```properties
# gradle.properties
org.gradle.parallel=true
```
