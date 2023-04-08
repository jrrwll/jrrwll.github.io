
---
title: "Maven"
---

## test

```shell
# org.apache.maven.plugins:maven-surefire-plugin:2.22.0
mvn -Dtest=TestApp1,TestApp2 test
mvn -Dtest=TestApp1#testHello* test
# match pattern 'testHello*' and 'testMagic*' 
mvn -Dtest=TestApp1#testHello*+testMagic* test
```

## dependency

```xml
<dependency>
  <groupId>com.sun</groupId>
  <artifactId>tools</artifactId>
  <version>1.8.0_212</version>
  <scope>system</scope>
  <systemPath>${env.JAVA_HOME}/lib/tools.jar</systemPath>
</dependency>
```
