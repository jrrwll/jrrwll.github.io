
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

## issue

### ​inconsistent module metadata found.

```groovy
maven {
    url 'some_nexus_url'
    metadataSources { artifact() } // Look directly for artifact
    content {
        // Use this repository only for org.myorg:mypkg:1.0
        includeVersion("org.myorg", "mypkg", "1.0")
    }
}
```
