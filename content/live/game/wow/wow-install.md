
---
title: "魔兽世界编译指南"
---

## TrinityCore

### 准备工作

- **Windows 10 1903 (19H1) 及以上版本**
- **Boost >= 1.78 (建议最新版)**
- **MySQL >= 5.7 (建议8.0)**
- **OpenSSL >= 1.1.0**
- **CMake >= 3.18.4 (建议最新版)**
- **MS Visual Studio Community >= 1.72 2022桌面版 勾选C++桌面开发**

#### Boost

https://www.boost.org/users/download/

https://boostorg.jfrog.io/artifactory/main/release/1.74.0/binaries/

安装至 C:\Apps\Lib\boost_1_84_0\

可选：添加系统变量 **`BOOST_ROOT`** = **`C:/Apps/Lib/boost_1_84_0`**

#### OpenSSL

> https://wiki.openssl.org/index.php/Binaries
> https://slproweb.com/products/Win32OpenSSL.html
> choose **Win64 OpenSSL v3.0.12**
> https://slproweb.com/download/Win64OpenSSL-3_0_12.exe
> https://slproweb.com/download/Win64OpenSSL-3_0_13.exe

MySQL 8.0.34+ 使用 OpenSSL 3，8.0-8.0.33 使用 OpenSSL 1.1.1

**Copy OpenSSL DDLs to: the OpenSSL binaries(/bin) directory**

#### Other

https://cmake.org/download/

https://dev.mysql.com/downloads/installer/ server only

委托vs安装 or https://git-scm.com/download/win

### 源码编译

```shell
git clone -b 3.3.5 https://github.com/TrinityCore/TrinityCore.git
git checkout 3.3.5
```

## AzerothCore

> https://www.azerothcore.org/wiki/windows-requirements

- OpenSSL 使用 Win64OpenSSL-3_0_12 版本
- 数据库不需要单独下载，直接运行worldserver.exe会自动从下载的源码中安装数据库

```shell
git clone https://github.com/azerothcore/azerothcore-wotlk.git --depth=1
```

### 编译

#### 配置和使用 CMake 生成 Visual C++ 解决方案

- 点击“Browse Source...” -> 选择源目录（git clone的AC源码目录）
- 点击“Browse Build...” -> 选择构建目录（新建一个目录用来编译程序）
- 点击“Configure”
- 确保“Use default native compilers”被勾选（默认应该已勾选）。
- 在下拉菜单中，选择在“所需软件”部分下载的编译器版本为"Visual Studio 17 2202"。
- 点击“Finish”。
- 确保“TOOLS_BUILD”选为all。这将编译稍后设置中需要的地图提取工具。
- 再次点击“Configure”。只要在日志窗口中有以红色输入的错误，您就需要检查您的参数并再次点击它。
- 点击“Generate”。这将安装所选的构建文件到您的Build文件夹中。
- 在CMake窗口中使用按钮[Open Project]，直接在VS中打开解决方案。

#### 在VS上执行编译

- 在 CMake 窗口中使用 [Open Project] 按钮直接打开解决方案到 VS。
- 在顶部的菜单上，把 Debug，修改为 Release。
- 右键单击 ALL_BUILD，然后选择 生成。
- 最终编译产物位于：\bin\Release

**复制DLL文件到编译的服务端程序目录**

- C:\Program Files\MySQL\MySQL Server 8.0\lib：
`libmysql.dll`

- C:\Program Files\OpenSSL-Win64\bin：
`legacy.dll`
`libcrypto-3-x64.dll`
`libssl-3-x64.dll`

**重命名configs目录中的文件**

```bat
rename worldserver.conf.dist worldserver.conf
rename authserver.conf.dist authserver.conf
```

### 服务器设置

#### 提取客户端数据

> 下载链接：https://wowdl.net/client/World-of-Warcraft-3.3.5a.12340-zhCN （纯净版，缺少巫妖王之怒相关动画文件）
> http://wow.gameivy.com/zhCN/WoW_v3.3.5_zhCN.zip

复制服务端下述文件到客户端根目录(Wow.exe 和 Data 文件夹所在位置)中

```bat
rem \bin\Release
mapextractor.exe
mmaps_generator.exe
vmap4extractor.exe
vmap4assembler.exe
rem azerothcore-wotlk\apps\extractor
extractor.bat
```

复制源码azerothcore-wotlk\apps\extractor目录中的`extractor.bat`到客户端根目录。然后运行并选择您的提取选项。对于全新设置，应选择选项 4 - 提取全部。

提取完成之后，将 vmaps、maps、dbc、cameras和mmaps文件夹移到与 AzerothCore 二进制文件worldserver.exe相同的目录中。

#### 创建数据库

默认情况下，AzerothCore 需要运行以下三个数据库：
- acore_auth：包含帐户数据 - 用户名、密码、GM 访问、领域信息等。
- acore_characters：包含角色数据 - 创建的角色、库存、银行物品、拍卖行、工单等。
- acore_world：包含游戏体验内容，如NPC、任务、物体等。

```sql
-- 执行源码目录 azerothcore-wotlk/data/sql/create 中的create_mysql.sql文件
source create_mysql.sql
```

#### 启动 worldserver(必须在创建好数据库之后)

启动 worldserver，当它询问是否要创建数据库时按 Enter 键。在完成此步骤之前，请不要启动您的 Authserver。(worldserver.conf的mysql登录凭证和SourceDirectory默认情况下是设置正确的)

#### 网络配置

- 开放端口 3724 和 8085
- Realmlist 表 IP设置
  - 127.0.0.1：在运行 WoW 的同一台计算机上安装了 AzerothCore，并且只有您连接到它
  - LAN IP (192.168.x.x)：将 AzerothCore 安装在与运行 WoW 的计算机不同的计算机上，但所有涉及的计算机都位于同一网络（路由器）上，则使用该计算机的局域网 IP
  - 公网IP或域名：希望其他人连接到您的服务器

```sql
use acore_auth;
update realmlist set address = 'your_ip' where id = 1;
```

#### 运行 Authserver

authserver 允许您输入用户名/密码以连接到领域（游戏服务器）选择屏幕，而 worldserver 允许您连接到领域（游戏服务器）本身。一旦您进入游戏，如果您不希望其他人连接到服务器，可以安全地关闭 authserver。

#### 创建登录账户

> 在 worldserver 窗口下输入GM指令

**`account set gmlevel $account #level #realmid`**

#level 可以是 0-3，#realmid 是领域 ID。将 #level 设置为 "3" 是 GM 账户级别（更高的数字表示更多权限），而 "-1" 是领域 ID，表示 "所有领域"。

`acore_world.command` 表 name security help：如果一个命令的安全级别为 2，那么所有 GM 级别为 2、3 和 4 的账户都可以使用它

```shell
# 创建账户
account create tuke tuke
# 设置账户级别 所有领域 GM
account set gmlevel tuke 3 -1
# 查看指令帮助信息
help
```

#### 客户端设置

- 修改 World of Warcraft\Data\zhCN\realmlist.wtf，确保文件中的IP和realmlist设置的IP一致：`set realmlist 127.0.0.1`
- （可选）如果您希望使用 WoW Launcher.exe 运行客户端，则必须将 set patchlist 更改为与您的 realmlist 相同的 IP/dns 名称。或者直接在WTF目录下改`Config.wtf`文件：

```conf
SET locale "zhCN"
SET realmList "127.0.0.1"
SET hwDetect "0"
SET gxRefresh "144"
SET gxMultisampleQuality "0.000000"
SET gxFixLag "0"
SET videoOptionsVersion "3"
SET movie "0"
SET Gamma "1.000000"
SET readTOS "1"
SET readEULA "1"
SET showToolsUI "1"
SET Sound_OutputDriverName "System Default"
SET Sound_MusicVolume "0.40000000596046"
SET Sound_AmbienceVolume "0.60000002384186"
SET farclip "397"
SET specular "1"
SET groundEffectDensity "24"
SET projectedTextures "1"
SET mouseSpeed "1"
SET accounttype "LK"
SET Sound_EnableReverb "1"
SET Sound_EnableSoftwareHRTF "1"
SET Sound_EnableHardware "1"
SET realmName "Trinity"
SET gxResolution "3840x2160"
SET gameTip "1"
```

#### 登录游戏

双击 `Wow.exe` 启动你的客户端，输入账号密码即可进入游戏

### 注意事项

#### `worldserver.conf`

```conf
#
#    CharactersPerAccount
#        Description: Limit number of characters per account on all realms on this realmlist.
#        Important:   Number must be >= CharactersPerRealm
#        Default:     50

CharactersPerAccount = 50

#
#    CharactersPerRealm
#        Description: Limit number of characters per account on this realm.
#        Range:       1-10
#        Default:     10 - (Client limitation)

CharactersPerRealm = 10

#
#    HeroicCharactersPerRealm
#        Description: Limit number of heroic class characters per account on this realm.
#        Range:       1-10
#        Default:     1

HeroicCharactersPerRealm = 1

#
#    CharacterCreating.MinLevelForHeroicCharacter
#        Description: Limit creating heroic characters only for account with another
#                     character of specific level (ignored for GM accounts)
#        Default:     55 - (Enabled, Requires at least another level 55 character)
#                     0  - (Disabled)
#                     1  - (Enabled, Requires at least another level 1 character)

CharacterCreating.MinLevelForHeroicCharacter = 55

#
#    StartPlayerLevel
#        Description: Starting level for characters after creation.
#        Range:       1-MaxPlayerLevel
#        Default:     1

StartPlayerLevel = 1

#
#    StartHeroicPlayerLevel
#        Description: Staring level for heroic class characters after creation.
#        Range:       1-MaxPlayerLevel
#        Default:     55

StartHeroicPlayerLevel = 55

#
#    SkipCinematics
#        Description: Disable cinematic intro at first login after character creation.
#                     Prevents buggy intros in case of custom start location coordinates.
#        Default:     0 - (Show intro for each new character)
#                     1 - (Show intro only for first character of selected race)
#                     2 - (Disable intro for all classes)

SkipCinematics = 0

#
#    StartPlayerMoney
#        Description: Amount of money (in Copper) that a character has after creation.
#        Default:     0
#                     100 - (1 Silver)

StartPlayerMoney = 0

#
#    StartHeroicPlayerMoney
#        Description: Amount of money (in Copper) that heroic class characters have after creation.
#        Default:     2000
#                     2000 - (20 Silver)

StartHeroicPlayerMoney = 2000

#
#     PlayerStart.String
#        Description: String to be displayed at first login of newly created characters.
#         Default:    "" - (Disabled)

PlayerStart.String = ""

#
#    MaxPlayerLevel
#        Description: Maximum level that can be reached by players.
#        Important:   Levels beyond 100 are not recommended at all.
#        Range:       1-255
#        Default:     80

MaxPlayerLevel = 80

#
#    Rate.XP.Kill
#    Rate.XP.Quest
#    Rate.XP.Explore
#    Rate.XP.Pet
#        Description: Experience rates (outside battleground)
#        Default:     1 - (Rate.XP.Kill)
#                     1 - (Rate.XP.Quest)
#                     1 - (Rate.XP.Quest.DF) - Dungeon Finder/LFG quests only.
#                     1 - (Rate.XP.Explore)
#                     1 - (Rate.XP.Pet)

Rate.XP.Kill      = 1
Rate.XP.Quest     = 1
Rate.XP.Quest.DF  = 1
Rate.XP.Explore   = 1
Rate.XP.Pet       = 1

#
#    Rate.Drop.Item.Poor
#    Rate.Drop.Item.Normal
#    Rate.Drop.Item.Uncommon
#    Rate.Drop.Item.Rare
#    Rate.Drop.Item.Epic
#    Rate.Drop.Item.Legendary
#    Rate.Drop.Item.Artifact
#    Rate.Drop.Item.Referenced
#    Rate.Drop.Money
#        Description: Drop rates for money and items based on quality.
#        Default:     1 - (Rate.Drop.Item.Poor)
#                     1 - (Rate.Drop.Item.Normal)
#                     1 - (Rate.Drop.Item.Uncommon)
#                     1 - (Rate.Drop.Item.Rare)
#                     1 - (Rate.Drop.Item.Epic)
#                     1 - (Rate.Drop.Item.Legendary)
#                     1 - (Rate.Drop.Item.Artifact)
#                     1 - (Rate.Drop.Item.Referenced)
#                     1 - (Rate.Drop.Money)

Rate.Drop.Item.Poor             = 1
Rate.Drop.Item.Normal           = 1
Rate.Drop.Item.Uncommon         = 1
Rate.Drop.Item.Rare             = 1
Rate.Drop.Item.Epic             = 1
Rate.Drop.Item.Legendary        = 1
Rate.Drop.Item.Artifact         = 1
Rate.Drop.Item.Referenced       = 1
Rate.Drop.Money                 = 1
```
