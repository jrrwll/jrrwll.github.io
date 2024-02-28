
---
title: "Apps on Mac"
---

## built-in

### 剪贴板

Finder->编辑->显示剪贴板

```shell
osascript -e 'clipboard info'
```

## ssh-server

```shell
# comment the following line
# ForceCommand /usr/local/bin/ssh_session
vim /etc/sshd_config

# start sshd
sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist
sudo launchctl unload -w /System/Library/LaunchDaemons/ssh.plist
sudo launchctl list | grep ssh
```

## VirtualBox

```shell
# Security Settings，enable any source
sudo spctl --master-disable

# enable kexts from loading for VirtualBox
# sudo /Library/StartupItems/VirtualBox/VirtualBox restart
sudo "/Library/Application Support/VirtualBox/LaunchDaemons/VirtualBoxStartup.sh" restart
```

## path-finder
> https://xclient.info/s/path-finder.html

