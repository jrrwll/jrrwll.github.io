
---
title: "Maven"
---

## awesome

```shell
alias mvnc="mvn clean -DskipTests=true -Dmaven.source.skip=true -Dsource.skip=true"
alias mvnp="mvn package -DskipTests=true -Dmaven.source.skip=true -Dsource.skip=true"
alias mvni="mvn install -DskipTests=true"
alias mvnd="mvn package -DskipTests=true"

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

```shell
// 列出项目的所有jar包
mvn dependency:list -Dverbose

// -Dincludes=[groupId]:[artifactId]:[type]:[version]
// -Dexcludes=velocity:velocity,:::*-SNAPSHOT
mnv dependency:tree -Dverbose

// 分析依赖
mnv dependency:analyze-only

// 查找<dependencies/> 和 <dependencyManagement/>中重复声明的依赖
mnv dependency:analyze-duplicate

// 列出所有的远程repositories
dependency:list-repositories

// 清理本地依赖，重新从远程repository下载
// -DactTransitively=false 仅对项目的直接依赖进行操作
// -DmanualIncludes 自定义依赖
dependency:purge-local-repository
```
