
---
title: "Git"
---

## git

```shell
# mkdir blog && cd blog
# git init
# git remote add origin git@github.com:tukeof/tukeof.github.io.git
git clone git://github.com/tukeof/tukeof.github.io.git blog
cd blog
git add *.md
git commit -m 'initial project version'
git push -u origin master
```

## remote

```shell
# 添加一个新的远程Git存储库
git remote add <shortname> <url>
git remote add origin git@github.com:tukeof/tukeof.github.io.git
git remote add origin ssh://username@127.0.0.1/reponame

git remote -v
git remote update
# 也会改变所有远程跟踪分支名称
git remote rename branch1 branch2
git remote rm branch1

git remote show origin

# 在本地新建一个tmp分支，并将远程origin仓库的master分支代码下载到本地tmp分支
git fetch origin master:tmp 
# 比较本地代码与刚刚从远程下载下来的代码的区别
git diff tmp 
# 合并temp分支到本地的master分支
git merge tmp
# 删除tmp分支
git branch -d tmp
# 取回远程主机某个分支的更新，再与本地的指定分支合并
git pull origin master:<branch_name>
```

## add, rm, commit, log

```shell
# -a，--all，自动暂存已修改和删除的文件
git commit -a -m 'message'

# 从工作目录和暂存区域中删除文件
git rm <file>
# 仅从暂存区域中删除目录
git rm -r -cached <dir>

git mv README.md README
# mv README.md README
# git rm README.md
# git add README

# --amend
# 占用当前暂存区域并将其用于提交。如果自上次提交后没有进行任何更改，那么快照将看起来完全相同，并且将更改的是提交消息。
git commit --amend

# 撤销对暂存区的更改
git reset HEAD <file>
# 丢弃工作目录中的更改
git checkout - <file>
```

## svn

```shell
# clone
svn co https://localhost/user/repo

svn co https://localhost/username/repo -r some_version
svn checkout https://localhost/username/repo/dir1/dir2/proj1
```

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

### Phabricator

```shell
// apply patch to current branch
arc patch --diff <patch_version_number> --nocommit --nobranch
```
