
---
title: "Spark Param"
---

```sql
# 推测功能，检测每个Task的执行时间，如果Task的执行时间超过整个Stage所有Task平均执行时间的多少倍(spark.speculation.multiplier)，
# 会启动另外一个Task处理相同数据，两个Task取最前完成的那个Task处理结果，另一个Task被Kill
set spark.speculation=true;
```
