
---
title: "Maven Plugins"
---

## package

### jar

```xml
<!-- https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-jar-plugin -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>3.3.0</version>
    <configuration>
        <archive>
            <manifest>
                <mainClass>com.example.Main</mainClass>
                <!-- add Class-Path to Manifest -->
                <addClasspath>true</addClasspath>
                <classpathPrefix>./lib/</classpathPrefix>
                <!-- 依赖是否使用带有时间戳的唯一版本号 -->
                <useUniqueVersions>false</useUniqueVersions>
            </manifest>
        </archive>
    </configuration>
</plugin>
```

### dependency

```xml
<!-- https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-dependency-plugin -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>3.6.1</version>
    <executions>
        <!-- 把当前项目的所有依赖放到target目录下的lib文件夹下 -->
        <execution>
            <id>copy</id>
            <phase>package</phase>
            <goals>
                <goal>copy-dependencies</goal>
            </goals>
            <configuration>
                <outputDirectory>${project.build.directory}/lib</outputDirectory>
            </configuration>
        </execution>
    </executions>
</plugin>
```

### assembly

```xml
<!-- https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-assembly-plugin -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>3.7.1</version>
    <configuration>
        <archive>
            <manifest>
                <mainClass>com.example.Main</mainClass>
            </manifest>
        </archive>
        <!-- assembly:single: xxx-jar-with-dependencies.jar-->
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
        <finalName>${project.name}</finalName>
    </configuration>
    <executions>
        <!-- 在执行package时，执行assembly:single -->
        <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

**assembly.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<assembly xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.1"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.1 http://maven.apache.org/xsd/assembly-1.1.1.xsd">
    <!--
    <descriptors>
        <descriptor>assembly.xml</descriptor>
    </descriptors>
    -->
    <formats>
        <format>zip</format>
        <!-- format>tar.gz</format -->
        <!-- format>dir</format -->
    </formats>
    <dependencySets>
        <dependencySet>
            <unpack>false</unpack>
            <outputDirectory>lib</outputDirectory>
            <useTransitiveFiltering>true</useTransitiveFiltering>
            <useStrictFiltering>true</useStrictFiltering>
            <fileMode>666</fileMode>
            <includes>
                <include>org.hibernate:*</include>
            </includes>
            <!-- 将scope为runtime的依赖包打包到lib目录下 -->
            <scope>runtime</scope>
        </dependencySet>
    </dependencySets>
    <fileSets>
        <fileSet>
            <!-- 定义代码目录中与输出目录的映射 -->
            <directory>${project.basedir}/src/main/resources</directory>
            <outputDirectory>/</outputDirectory>
            <filtered>false</filtered>
        </fileSet>
    </fileSets>
</assembly>
```

### shade

```xml
<!-- https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-shade-plugin -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>3.5.2</version>
    <executions>
        <execution>
            <phase>package</phase>
            <goals>
                <goal>shade</goal>
            </goals>
            <configuration>
                <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>com.example.biz.Launcher</mainClass>
                    </transformer>
                    <!-- 对文件内容追加合并 -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                        <resource>META-INF/spring.schemas</resource>
                    </transformer>
                </transformers>
            </configuration>
        </execution>
    </executions>
</plugin>
```
