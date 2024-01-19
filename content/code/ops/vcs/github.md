
---
title: "Github"
---

## mirror

```shell
# set
git config –global url."https://hub.fastgit.org/".insteadOf "https://github.com/"
git config protocol.https.allow always

git config –global –list
# unset
git config –global –unset url.https://github.com/.insteadof
```

### https://mirror.ghproxy.com

```shell
git clone https://mirror.ghproxy.com/https://github.com/user/repo
wget https://mirror.ghproxy.com/https://github.com/user/repo/archive/master.zip
wget https://mirror.ghproxy.com/https://github.com/user/repo/master/file
```

### https://gitee.com/mirrors

```shell
git clone https://gitee.com/mirrors/repo.git
```

### https://gitclone.com

```shell
git clone  https://gitclone.com/github.com/user/repo,git
```

### https://gitmirror.com/

**raw.githubusercontent.com**

https://raw.gitmirror.com/user/repo/master/file, blocked: .(zip|rar|7z|apk|ipa|exe|msi|m3u|m3u8|mp4|mp3)

### https://githubfast.com

```shell
git clone https://githubfast.com/user/repo
```
