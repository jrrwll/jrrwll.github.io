
---
title: "Clean Mac"
---

## OS

```shell
# 清理内存
sudo purge
# 系统日志文件
sudo rm -rf /private/var/log/*
# finder快速查看的缓存
sudo rm -rf /private/var/folders/
sudo rm -rf ~/Library/Caches/*
cd /private/var/tmp/
sudo rm -rf TM*
```

## ContextMenu

```shell
# 右键打开方式残余条目清理
/System/Library/Frameworks/CoreServices.framework/\
Versions/A/Frameworks/LaunchServices.framework/\
Versions/A/Support/lsregister -kill -r \
-domain local -domain user
killall Finder
echo "Open With has been rebuilt, Finder will relaunch"
```

## LaunchPad

### Delete icon

```sh
Finder: command+shift+G  /private/var/folders/
Search: com.apple.dock.launchpad  folders

cd /private/var/folders/dd/*/0/com.apple.dock.launchpad/db
sqlite3 db "select * from apps"
sqlite3 db "delete from apps where title='YourApp';" && killall Dock

# reset LaunchPad & Dock
defaults write com.apple.dock ResetLaunchPad -bool true && killall Dock
```

### Delete postwoman from lauthpad

```sh
rm -rf ~/Applications/Chrome Apps.localized/Postwoman.app
defaults write com.apple.dock ResetLaunchPad -bool true && killall Dock
```

## Uninstall

### Microsoft AutoUpdate

```shell
sudo chmod 000 /Library/Application\ Support/Microsoft/MAU2.0/Microsoft\ AutoUpdate.app

# Microsoft AutoUpdate
sudo rm -rf /Library/Application\ Support/Microsoft/MAU2.0
# Edge
sudo rm -rf /Library/Application\ Support/Microsoft/EdgeUpdater
```

### Matlab

```sh
rm -rf /Applications/Polyspace
# reset LaunchPad & Dock
defaults write com.apple.dock ResetLaunchPad -bool true && killall Dock

APPLICATION_NAME=MathWorks
rm -rf ~/Library/Application\ Support/${APPLICATION_NAME?}
rm -rf ~/Library/Caches/${APPLICATION_NAME?}
rm -rf ~/Library/Containers/${APPLICATION_NAME?}
rm -rf ~/Library/Cookies/${APPLICATION_NAME?}
rm -rf ~/Library/LaunchAgents/${APPLICATION_NAME?}
rm -rf ~/Library/LaunchDaemons/${APPLICATION_NAME?}
rm -rf ~/Library/Logs/${APPLICATION_NAME?}
rm -rf ~/Library/Preferences/${APPLICATION_NAME?}
rm -rf ~/Library/PreferencePanes/${APPLICATION_NAME?}
```

### Docker

```sh
sudo rm /Library/PrivilegedHelperTools/com.docker.vmnetd
sudo rm /Library/LaunchDaemons/com.docker.vmnetd.plist

rm -rf ~/Library/Application\ Support/Docker\ Desktop
rm ~/Library/Preferences/com.docker.docker.plist
rm -rf ~/Library/Saved\ Application\ State/com.electron.docker-frontend.savedState		
rm -rf ~/Library/Group\ Containers/group.com.docker
rm -rf ~/Library/Logs/Docker\ Desktop
rm ~/Library/Preferences/com.electron.docker-frontend.plist
rm -rf ~/Library/Cookies/com.docker.docker.binarycookies
```
