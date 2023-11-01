
---
title: "Config Mac"
---

## OS

```shell
hostname
echo $HOSTNAME
scutil --get ComputerName
scutil --get HostName

# 主机名与计算机名的修改
sudo scutil --set ComputerName mac
sudo scutil --set HostName mac
```

### Font

```shell
/System/Library/Fonts
/Library/Fonts
$HOME/Library/Fonts
```

### ARM

#### Windows ARM

[How to Download and Install Windows 11 on Arm with ISO? (minitool.com)](https://www.minitool.com/data-recovery/download-install-windows-11-arm-iso.html)

[UUP dump](https://uupdump.net/)

## Security

```shell
# Allow single app to bypass the gatekeeper in Mac
sudo xattr -r -d com.apple.quarantine /Applications/Adobe\ Zii\ 2020\ 5.1.8.app
```

## Uniform Type Identifiers

Mac 下的文件类型被定义为 Uniform Type Identifiers

https://developer.apple.com/library/mac/documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html

- all: application handles all roles for the given UTI.
- viewer: application handles reading and displaying documents with the given UTI.
- editor: application can manipulate and save the item. Implies viewer.
- shell: application can execute the item.
- none: application cannot open the item, but provides an icon for the given UTI.

### duti

- **-s**:  从命令行读参数，用以设置默认程序
- **-x ext**: 选项会让 duti 输出参数中扩展名对应的默认应用
- **-d uti**: 打印 UTI 的默认应用.
- **-l uti**: 打印 UTI 的所有可用应用.
- -V: 打印版本.
- -v: 详细输出.
- -h: 帮助.

```shell
brew install duti

# 列出文件的 UTI
# kMDItemContentType    net.daringfireball.markdown
mdls some.md

# 获取 Mac 应用的 Bundle ID
osascript -e 'id of app "Finder"'
# abnerworks.Typora
osascript -e 'id of app "Typora"'

cat > duti.conf <<EOF
abnerworks.Typora    net.daringfireball.markdown    editor
abnerworks.Typora    com.apple.log                  editor
# abnerworks.Typora    public.plain-text              editor
EOF
# duti -s com.apple.Safari public.html all
# 读取配置文件的内容，用以设置默认程序
duti duti.conf
```

## Finder

```shell
# 增强预览工具
brew install qlcolorcode qlstephen qlmarkdown quicklook-json quicklook-csv qlimagesize
```

```shell
# 禁用更改扩展名的提示
defaults write com.apple.finder FXEnableExtensionChangeWarning \
-bool false
killall Finder
```

## 快捷键

ctrl + w —往回删除一个单词，光标放在最末尾 
ctrl + k —往前删除到末尾，光标放在最前面（可以使用ctrl+a） 
ctrl + u 删除光标以前的字符 
ctrl + k 删除光标以后的字符 
ctrl + a 移动光标至的字符头 
ctrl + e 移动光标至的字符尾 
ctrl + l 清屏

Command+Shift+3 
Command+Shift+4，光标将变为小交叉十字图标
Command+Shift+4并按下空格键，交叉十字图标将变为小照相机图标
Command+Control+Shift+3，图片将保存在粘贴板

## ._ files

```sh
# disable .DS_Store file creation on network drives 
defaults write com.apple.desktopservices DSDontWriteNetworkStores true
```
