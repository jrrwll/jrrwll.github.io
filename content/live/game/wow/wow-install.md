
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

**复制DLL文件到编译的服务端程序目录**

- C:\Program Files\MySQL\MySQL Server 8.x\lib：
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

#### 客户端配置

> 下载链接：http://wow.gameivy.com/zhCN/WoW_v3.3.5_zhCN.zip

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

#### 数据库配置

默认情况下，AzerothCore 需要运行以下三个数据库：
- acore_auth：包含帐户数据 - 用户名、密码、GM 访问、领域信息等。
- acore_characters：包含角色数据 - 创建的角色、库存、银行物品、拍卖行、工单等。
- acore_world：包含游戏体验内容，如NPC、任务、物体等。

```sql
-- 执行源码目录 azerothcore-wotlk/data/sql/create 中的create_mysql.sql文件
source create_mysql.sql
```
