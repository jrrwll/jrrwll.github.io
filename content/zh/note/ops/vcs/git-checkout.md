
---
title: "Git Checkout"
---

## branch

```shell
# list all branch, include local and remote
git branch -a
# delete branch
git branch -d <local_branch>
git push origin --delete <remote_branch>

# list tags
git log --pretty=oneline --abbrev-commit
git show <tag_name>
# tag a tag based on a commit, default cid is `HEAD`
git tag <tag_name> <commit_id>
git tag -a <tag_name> -m "message for tag" <commit_id>

# dangerous operation, it's better no-modification until checkout to `HEAD` 
git checkout <tag_name>
# 
git checkout -b <bra_name> <tag_name>

git tag -l "<prefix>*"
git push origin <tag_name>
# push all tags
git push origin --tags

# delete a local tag
git tag -d <tag_name>
# delete a remote tag
git push origin :refs/tags/<tag_name>
```

## stash

```shell
# list the stash entries that you currently have. stash@{0} is the latest entry
git stash list
# save all uncommitted changes
git stash
# apply all uncommitted changes
git stash apply
```

## merge-base

```shell
git merge-base origin/master HEAD
```
