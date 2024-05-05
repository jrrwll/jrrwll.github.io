
---
title: "date"
---

## date

```shell
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

## cal

```shell
# print cal
cal
cal 9 1752
```

## timedatectl

```shell
timedatectl list-timezones
timedatectl set-timezone UTC
timedatectl set-timezone Asia/Shanghai

timedatectl set-time "2000-01-01 00:00:00"

timedatectl set-ntp true
```

### ntpdate

```shell
sudo apt install ntpdate

ntpdate ntp.aliyun.com
```

