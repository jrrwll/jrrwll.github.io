
---
title: "魔兽世界编译指南"
---

## 准备工作

- **Windows 10 1903 (19H1) 及以上版本**
- **Boost >= 1.78 (建议最新版)**
- **MySQL >= 5.7 (建议8.0)**
- **OpenSSL >= 1.1.0**
- **CMake >= 3.18.4 (建议最新版)**
- **MS Visual Studio Community >= 1.72 2022桌面版 勾选C++桌面开发**

### Boost

https://www.boost.org/users/download/

解压至 C:\Apps\Lib\boost_1_84_0\

添加系统变量 **`BOOST_ROOT`** = **`C:/Apps/Lib/boost_1_84_0`**

### OpenSSL

MySQL 8.0.34+ 使用 OpenSSL 3，8.0-8.0.33 使用 OpenSSL 1.1.1

## 源码编译

```shell
git clone -b 3.3.5 https://github.com/TrinityCore/TrinityCore.git
git checkout 3.3.5
```
