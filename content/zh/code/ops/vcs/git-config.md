
---
title: "Git Config"
---

## config

```shell
git help config
man git-config
git config --list

# 提交时转换为LF，检出时转换为CRLF
git config --global core.autocrlf true
# 提交时转换为LF，检出时不转换
git config --global core.autocrlf input
# 提交检出均不转换
git config --global core.autocrlf false

# 拒绝提交包含混合换行符的文件
git config --global core.safecrlf true
# 允许提交包含混合换行符的文件
git config --global core.safecrlf false
# 提交包含混合换行符的文件时给出警告
git config --global core.safecrlf warn

git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.s status

git config --global alias.unstage 'reset HEAD - '
git config --global alias.last 'log -1 HEAD'
# git config --global alias.l '!ls -lah'
git config --global alias.url 'remote get-url --all origin'

# git config --global core.editor "'C:/Options/Notepad++/notepad++.exe' -multiInst -nosession"
git config --global core.editor vim
```

### `.gitconfig`

```ini
[user]
	name = yourname
	email = youremail
[http]
    #proxy = socks5://127.0.0.1:1080
[https]
    #proxy = socks5://127.0.0.1:1080	
[core]
    autocrlf = input
    safecrlf = true
[alias]
    co = checkout
    br = branch
    ci = commit
    s = statcus
    last = log -1 HEAD
    lg = log --graph
    url = remote get-url --all origin  
```

## `.ssh/config`

```shell
ssh-keygen -t rsa -b 4096 -C "youremail"
```

```conf
host *
    AddKeysToAgent yes
    UseKeychain yes
    TCPKeepAlive yes
    ServerAliveInterval 60
    ServerAliveCountMax 5
    
host github.com
  user git
  hostname github.com
  port 22
  identityfile ~/.ssh/id_rsa
```
