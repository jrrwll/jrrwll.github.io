
---
title: "File"
---

## split

```shell
# 指定文件大小来分割，输出 output-aa、output-ab、……、output-zz
split -b 10m big_file output-
# 指定行数，输出 xaa、……、xzz
split -l 1000 big_file
```

