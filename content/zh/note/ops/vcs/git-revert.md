
---
title: "Git Revert"
---

## revert

```shell
# revert to previous commit
git revert <previous-commit>
```

## reset

```shell
# discard changes and reset files to master
git reset --hard origin/master

git reset --hard HEAD
# or save changes
git reset --soft HEAD 

# discard changes and reset to current branch
git checkout . && git clean -xdf
```
