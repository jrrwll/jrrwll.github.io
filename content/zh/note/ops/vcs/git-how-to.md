
---
title: "Git How-To"
---

## undo `untracked`

```shell
git reset --hard <commit_id>

# show which will be removed, include untracked directories
# -n --dry-run， Don't actually remove anything
git clean -d -n
# delete anyway, no confirm
git clean -d -f
```

## undo `not staged change` (removal or modification)

```shell
# in stage space but changed by external
# touch a && gti add a && rm a, then a has not staged changes

# discard changes in working directory
git restore <files>
# or
git checkout <files>
# update what will be committed
git add <files>

# caused by removal
git rm <files>
# or
git rm --cached <files>
```

## undo `add`

```shell
# undo stage
git restore --staged <files>

# remove from staged space
git rm --cached d
```

## undo `commit`

```shell
# message
git commit --amend -m 'message which will cover the old one'

# undo commit, but not undo add
# --soft, do not touch the index file nor the working tree
# --hard, match the working tree and index to the given tree
# --mixed, reset the index but not the working tree (default)
git reset --soft HEAD^
# undo <n> commits
git reset --soft HEAD~n
```

## merge commit

```shell
# reapply n commits
git rebase -i HEAD~n

# pick
# squash
# ...
# squash

git add .
git rebase --continue
# git rebase --abort

git push --force
```

## delete the last commit

```shell
# where git interprets x^ as the parent of x 
# + as a forced non-fastforward push. 
git push master +<second-last-commit>^:master

# or
git reset HEAD^ --hard
git push master -f
```

## delete the second last commit

```shell
# this will open an editor and show a list of all commits since the commit we want to get rid of
# simply remove the line with the offending commit, likely that will be the first line
git rebase -i <second-last-commit>^

git push master -f
```