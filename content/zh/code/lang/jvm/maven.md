
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
