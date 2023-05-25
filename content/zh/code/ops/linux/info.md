
---
title: "Info"
---

## version

### os name

```shell
cat /usr/lib/os-release
cat /etc/issue
uname -a
```

## code 

```shell
tree -if | grep -v node_modules | egrep '[.](j|t)sx?$' | xargs wc -l
```
