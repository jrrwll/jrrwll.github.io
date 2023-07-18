
---
title: "User & Group"
---

## sudo

**/etc/sudoers**

```
root          ALL=(ALL)       ALL
%wheel        ALL=(ALL)       NOPASSWD: ALL
tuke          ALL=(ALL)       NOPASSWD: ALL
```

## useradd

```shell
groupadd sudo
getent group
cat /etc/group | grep sudo

# -m --create-home      Create Home Directory
# -d --home /opt/home   Specific Home Directory
# -u --uid 501
# -g --gid 20, -g tuke  Specific Existing Primary Group ID/Name
# -G --groups wheel,docker
#                       Assign Secondary Groups
# -s /usr/bin/zsh       Specific Login Shell
useradd -m tuke
passwd tuke
usermod -a -G sudo tuke
 
id -g
id -gn
grep tuke /etc/passwd
```
