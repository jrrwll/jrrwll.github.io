
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

### .git

- `git clone https://gitee.com/mirrors/repo.git`
- `git clone https://hub.fastgit.org/user/repo.git`
- `git clone  https://gitclone.com/github.com/user/repo,git`
- hub.gitmirror.com

### raw.githubusercontent.com

- https://raw.fastgit.org/user/repo/master/file
- https://raw.gitmirror.com/user/repo/master/file, blocked: .(zip|rar|7z|apk|ipa|exe|msi|m3u|m3u8|mp4|mp3)

### releases

- https://download.fastgit.org/user/repo/releases/file

