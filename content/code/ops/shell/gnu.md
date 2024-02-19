
---
title: "Gnu Tools"
---

## datetime

### date

```sh
# 9999-99-99 99:99:99
# 00-23 00-23 00-59
# %t tab char
# %I 00-12
# %j 000-366
# %D MM/dd/yy
# %T hh:mm:ss
date "+%Y-%m-%d %H:%M:%S"

# timestamp, in sec
date +%s

# GMT
LANG=en_US.UTF-8 date -u
LANG=en_US.UTF-8 TZ=GMT date
```

### cal

```sh
# print cal
cal
cal 9 1752
```
