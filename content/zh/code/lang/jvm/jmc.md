
---
title: "JMC"
---

## Java Mission Control

https://www.oracle.com/java/technologies/javase/products-jmc8-downloads.html

```shell
jcmd $pid VM.unlock_commercial_features
# 在20s之后开始分析，持续2min，结果保存在jfr文件中
jcmd $pid JFR.start name=$name settings=profile delay=20s duration=2m filename=$name.jfr
# 检查进度
jcmd $pid JFR.check
```
