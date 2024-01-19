
---
title: "Gradle"
---

## install

https://mirrors.cloud.tencent.com/gradle/gradle-8.4-all.zip

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

## dependency

### global exclude

```groovy
// https://docs.gradle.org/current/userguide/dependency_management.html
configurations.all { 
    exclude group: 'com.sun.xml.bind' 
    exclude group: 'commons-collections', module: 'commons-collections' 
}
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

### 找不到或无法加载主类 org.gradle.wrapper.GradleWrapperMain

缺失 `gradle/wrapper/gradle-wrapper.jar`
