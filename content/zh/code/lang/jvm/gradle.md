
---
title: "Gradle"
---

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
