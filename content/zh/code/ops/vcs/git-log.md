
---
title: "Git Log"
---

## status

```shell
git init --bare /path/to/repo/.git
# git remote add origin /path/to/repo/.git
git clone /path/to/repo

# 显示工作目录和当前HEAD提交之间存在差异的路径
git status
# --short
# A added.c
# M modified.cc
# R renamed.h
# D deleted.hh
git status -s
```

## diff

```shell
# 将工作目录中的内容与暂存区域中的内容进行比较
git diff
# --cached，将暂存更改与上次提交进行比较  
git diff --staged

git diff oldCommit..newCommit
```

## log

```shell
# 按反向时间顺序列出在该存储库中进行的提交
git log
# -p，patch  显示每次提交中引入的差异（补丁输出）
# -2  仅显示最后两个条目
# --stat 在每个提交条目下方打印一个已修改文件的列表，已更改的文件数以及这些文件中添加和删除的行数
# --shortstat 仅显示--stat命令中已更改/插入/删除行。
# --pretty=oneline 在一行上打印每个提交
git log --pretty=oneline
# 短哈希 - 作者名，日期 ：注释
git log --pretty=format:"%h - %an, %ar : %s"
# --graph 添加一个很好的小ASCII图，显示您的分支和合并历史记录
git log --pretty=format:"%h %s" --graph

git log -<n>
# --since, --after
# --until, --before
git log --since=2.weeks
git log --since="2008-01-15"
git log --since="2 years 1 day 3 minutes ago"

# --committer 执行提交的用户
# --author 作出修改的用户
# 在提交消息中搜索关键字
git  --author=<author> --grep="keyword"
# 可以指定--author和--grep搜索条件的多个实例
# 这将限制提交输出到与任何--author模式和任何--grep模式匹配的提交
# --all-match 会进一步将输出限制为与所有--grep模式匹配的提交

# 仅显示提交添加或删除与字符串匹配的代码的提交
git log -S function_name
```

### Useful options for `git log --pretty=format`

| Option | Description of Output                           |
| ------ | ----------------------------------------------- |
| `%H`   | Commit hash                                     |
| `%h`   | Abbreviated commit hash                         |
| `%T`   | Tree hash                                       |
| `%t`   | Abbreviated tree hash                           |
| `%P`   | Parent hashes                                   |
| `%p`   | Abbreviated parent hashes                       |
| `%an`  | Author name                                     |
| `%ae`  | Author email                                    |
| `%ad`  | Author date (format respects the --date=option) |
| `%ar`  | Author date, relative                           |
| `%cn`  | Committer name                                  |
| `%ce`  | Committer email                                 |
| `%cd`  | Committer date                                  |
| `%cr`  | Committer date, relative                        |
| `%s`   | Subject                                         |

