
---
title: "grep"
---

## grep

```shell
# -r 深度优先遍历 -R 贪婪递归
# -n 显示行号
# -e 搜索的正则
# -w 匹配整个单词
grep -rn . --exclude='*.jar' -e 'key'
```
