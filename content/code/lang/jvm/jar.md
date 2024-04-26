
---
title: "Jar"
---

## Main-Class

```shell
java -cp 'app.jar:lib/*' com.exmaple.Main

# loader.path 为可执行jar指定额外的加载路径
java -Dloader.path=./lib -jar app.jar
```
