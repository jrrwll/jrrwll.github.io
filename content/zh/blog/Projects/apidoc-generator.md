---
title: "apidoc-generator API文档生成器简介"
linkTitle: "API文档生成器"
date: 2020-12-24
weight: 1
---

## 一、概述
> 基于java源码中的注释(不限于多行或单行)和泛型解析，来生成api文档元数据和具体文档输出的工具

## 二、实现原理
```plantuml
@startuml
folder "数据源" {
   component [source源码] as src
   component [source源码对应的class和依赖] as classpath
}

package "Java源码解析" {
    component [Java源码解析 com.github.javaparser] as javaparser
    src --> javaparser
}

node "Java类型解析" {
    component [类型解析 databind-core] as type
    component [数据Mock databind-json] as mock
    component [带注释的Json序列化 ] as jwc
    classpath --> type
    type --> mock
    mock --> jwc
}

frame "文档渲染" {
    component [API Doc元数据] as doc
    component [Markdown] as md
    component [Swagger元数据] as sg

    javaparser --> doc
    type --> doc
    doc --> mock

    jwc --> md
    doc --> md

    doc --> sg
}

@enduml
```

## 三、接入姿势

### 3.1 项目侵入式接入
可以编写单元测试或单独的模块来接入

#### 3.1.1 gradle 依赖引入
```groovy
testImplementation 'org.dreamcat:apidoc-generator:0.1-SNAPSHOT'
```

#### 3.1.2 maven 依赖引入
```xml
<dependency>
    <groupId>org.dreamcat</groupId>
    <artifactId>apidoc-generator</artifactId>
  	<version>0.1-SNAPSHOT</version>
  	<scope>test</scope>
</dependency>
```

#### 3.1.3 单元测试示例
```java
import java.util.Collections;
import org.dreamcat.cli.generator.apidoc.ApiDocConfig;
import org.dreamcat.cli.generator.apidoc.ApiDocGenerator;
import org.dreamcat.cli.generator.apidoc.renderer.JsonWithCommentMDRenderer;
import org.junit.jupiter.api.Test;

class JavaDocTest {

    @Test
    void test() throws Exception {
        String srcDir = "/path/to/project/src/main/java";
        String javaFileDir = srcDir + "/com/exmaple/app/api";
        String basePackage = "com.exmaple.app.api";

        ApiDocConfig config = new ApiDocConfig();
        config.setBasePackage(basePackage);
        config.setSrcDirs(Collections.singletonList(srcDir));
        config.setJavaFileDirs(Collections.singletonList(javaFileDir));
        JsonWithCommentMDRenderer renderer = new JsonWithCommentMDRenderer(config);
        ApiDocGenerator<String> generator = new ApiDocGenerator<>(config, renderer);
        String doc = generator.generate();
        System.out.println(doc);
    }
}
```

## 四、输出姿势

### 4.1 带注释的Json Markdown文档输出

示例如下

#### 4.1.1  get complex
入参
```js
"pNzod0"
```

出参
```js
{
    "code": "69nWl00", //  response code
    "msg": "t505j000000", //  error message
    "data": {
        "id": "47K34100", // /! entity id
        "createdBy": 3302967472579308987, //     who created the record    
        "createdAt": "2021-12-20 20:18:56", // 
        "a": 22108551, //  a or a + bi
        "b": 630757701, //  b of a + bi
        "salt": [
            34
        ], //  salt to sign 
        "admired": [
            {
                "id": 6274417466735950286,
                "name": "rg704000",
                "gender": unknown
            }
        ] //      * the people who admire the number     
    } //  real data
}
```

#### 4.1.2  list complex
入参
```js
{
    "pageNo": 1616467239, //  page number, default is 1
    "pageSize": 1187568143, //  page size, default is 10
    "token": "05Ie60000000", //  the token to sign
    "userIds": [
        1360046618
    ] //  users who admire the number
}
```

出参
```js
{
    "code": "Z14dx00", //  response code
    "msg": "6d1B", //  error message
    "data": {
        "pageNo": 332708021, //  page number, default is 1
        "pageSize": 1363282705, //  page size, default is 10
        "list": [
            {
                "id": "0HN5X", // /! entity id
                "createdBy": 7819338319132650901, //     who created the record    
                "createdAt": "2021-12-20 20:18:56", // 
                "a": 2114287267, //  a or a + bi
                "b": 1421927723, //  b of a + bi
                "salt": [
                    3
                ], //  salt to sign 
                "admired": [
                    {
                        "id": 6733097960264216561,
                        "name": "2O9eO000000000",
                        "gender": female
                    }
                ] //      * the people who admire the number     
            }
        ], //  the list data per page
        "totalCount": 1593462666, //  the total count
        "totalPage": 585256653, //  the total page
        "summary": {
            "todayCount": 940383353038686031, //  count of today
            "last7dayCount": 2958055079033281580, //  count of last 7 day
            "bits": [
                367530979
            ] //  magic bits
        } //  the summary per page
    } //  real data
}
```

#### 4.1.3  create complex
入参
```js
{
    "a": 15466019106679717788876966621497800342, //  some of complex
    "b": 1.14164135352093225954791023656452E+616, //  some of complex
    "props": {
        "Q14iG0000": "VWsEa00000"
    } //  props for extra settings 
}
```

出参
```js
{
    "code": "k", //  response code
    "msg": "7RwO600", //  error message
    "data": "OgF6" //  real data
}
```

#### 4.1.4  update complex
入参
```js
{
    "a": 25500759197484633195280009055164974548, //  some of complex
    "b": 7.9757671995651600229832514042539E+615, //  some of complex
    "props": {
        "oiE6200000000": "Qg7hw0"
    } //  props for extra settings 
}
```

出参
```js
{
    "code": "PCHj10000", //  response code
    "msg": "bDtE00000000", //  error message
    "data": "c5OH2000" //  real data
}
```

#### 4.1.5  delete complex
入参
```js
"9y4Bo000000000"
```

出参
```js
{
    "code": "E", //  response code
    "msg": "AbocF000000000", //  error message
    "data": null //  real data
}
```