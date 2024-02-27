
---
title: "ElasticSearch"
---

## 兼容性

查询数据总条数 `$.hits.totalHits`
- ES5: 查询参数 `?rest_total_hits_as_int=true`
- ES7: 请求体 `$.track_total_hits = true`
