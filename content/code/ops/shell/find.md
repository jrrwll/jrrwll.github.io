
---
title: "find"
---

## find

### -maxdepth n -mindepth n

目录深度

### -mtime n[smhdw]

文件修改过了多久，默认单位d天

### -cmin [-|+]n

文件状态修改过了多久，默认单位m分，大于+n、小于-n、或者恰好等于n

### -delele

删除，深度优先，可删除普通文件，空目录，不对链接文件生效

### -exec file_or_expr;

执行程序，{} 表示当前文件的路径名，如果程序是表达式，需要分号;结尾，从shell执行find，则分号需要转义
例如：find -type f -name "*.log" -exec gzip {} \;

## exmaple

```crontab
# 每小时整点过十分钟的时候，执行一次日志压缩，
# 然后在五分钟之后，将日志压缩文件移动到指定目录，
# 并且定时清理该目录下的三天之前的日志压缩文件
10 * * * * cd /opt/logs && find . -type f -name "*.log" -cmin +60 -exec gzip {} \;
15 * * * * cd /opt/logs && find . -maxdepth 1 -type f -name "*.gz" -exec mv {} compressed/ \;
10 * * * * /usr/bin/find /data/volumes/nginx/log/compressed -type f -mtime +3 -delete;
```
