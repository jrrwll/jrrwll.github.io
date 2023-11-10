
---
title: "Maven"
---

## awesome

```shell
function mvng {
    if [ "$#" -lt  2 ]; then
        echo "require at least two args"
        echo "mvng <groupId> <artifactId> [<version>]"
        return 1
    fi
    mvn archetype:generate -DgroupId=$1 -DartifactId=$2 -Dversion=${3=1.0-SNAPSHOT} -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
}
```

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
