
---
title: "Arc"
---

## arc

```shell
# Phabricator
git clone https://github.com/phacility/libphutil.git
git clone https://github.com/phacility/arcanist.git

#  将 some_install_path/arcanist/bin 加入到$PATH变量中
# 权限认证
arc install-certificate
arc set-config editor "vim"

# 开分支(git branch)，或查看当前分支和关联的 revision
arc feature 
# 创建或更新 revision，范围是 origin/master 到最新提交，包括暂存区的内容
arc diff
# 所有 revision 和状态
arc list
# 当前分支上的 commit 会全部被 land， 会先 pull --rebase 再 push
arc land
# 查看 diff 的范围等信息
arc which

```

## Phabricator

```shell
// apply patch to current branch
arc patch --diff <patch_version_number> --nocommit --nobranch
```

