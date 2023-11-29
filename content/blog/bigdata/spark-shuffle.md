---
title: "Spark Shuffle"
date: 2023-11-11
---

## Shuffle

```plantuml
@startuml
rectangle Map as m
rectangle Shuffle as s
rectangle Reduce as r
m -right-> s
s -right-> r
@enduml
```

**会产生shuffle的算子**

| Repartition | ByKey         | Join          | Distinct |
| ----------- | ------------- | ------------- | -------- |
| repartition | groupByKey    | cogroup       | distinct |
| coalesce    | reduceByKey   | join          |          |
|             | aggreateByKey | leftOuterJoin |          |
|             | combineByKey  | intersection  |          |
|             | sortByKey     | subtract      |          |
|             | sortBy        | subtractByKey |          |
