---
title: "Inceptor"
---

## install 

```shell
docker run -itd --privileged --name tdh --hostname tdh \
-v $PWD/tdh:/opt/transwarp \
-p1081:8180 \
-p1088:1088 \
-p2182:2181 \
-p8021:8020 \
-p10001:10000 \
tdh-standalone:2023.5
```
