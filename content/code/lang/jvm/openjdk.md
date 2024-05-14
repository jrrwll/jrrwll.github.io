
---
title: "OpenJDK"
---

## install 

### download

https://mirrors.tuna.tsinghua.edu.cn/Adoptium/8/jdk/

https://learn.microsoft.com/en-us/java/openjdk/download#openjdk-8

### os

- **mac**
> mac arm: https://www.azul.com/downloads/?version=java-8-lts&os=macos&package=jdk#zulu

```shell
# openjdk8
brew tap homebrew/cask-versions
brew install --cask temurin8
```

- **centos**

```shell
# Installing Java 8 on CentOS 7
yum install java-1.8.0-openjdk -y
update-alternatives --config java
```

- **debian**

```shell
# https://adoptium.net/installation/linux/
sudo apt install -y wget apt-transport-https
sudo mkdir -p /etc/apt/keyrings
sudo wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | sudo tee /etc/apt/keyrings/adoptium.asc
sudo echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | sudo tee /etc/apt/sources.list.d/adoptium.list
sudo apt update
sudo apt install temurin-21-jdk
```

## compile

##### jdk18 on mac

```bash
git clone https://github.com/openjdk/jdk18.git --depth=1
# autoconf=2.71, ccache=4.6.3, freetype=2.12.1
brew install autoconf ccache freetype

bash ./configure --help
# --enable-debug : --with-debug-level=fastdebug --with-debug-level=slowdebug
# --with-jvm-variants=server
# --with-num-cores=8
# --with-memory-size=8192

# MacOS
# configure: error: No xcodebuild tool and no system framework headers found
sudo rm -rf /Library/Developer/CommandLineTools
sudo xcode-select --install
# jdk17+ require xcode itself
bash ./configure --with-debug-level=slowdebug --enable-ccache --disable-warnings-as-errors
make images
./build/macosx-x86_64-normal-server-slowdebug/jdk/bin/java -version
```

##### jdk8u

```sh
# jdk8u
bash ./configure --with-num-cores=8 --with-debug-level=slowdebug
```

##### jdk17 on debian10

```sh
# 10.3 is ok
sudo apt install g++-10

# Compile jdk17 on debian 11.2 gcc 10.2
git clone https://github.com/openjdk/jdk17 --depth=1
cd jdk17u

# tools
sudo apt install gcc g++ make autoconf ccache zip 

# Boot JDK
sudo apt install openjdk-17-jdk

# header files
sudo apt install -y libx11-dev libxext-dev libxrender-dev libxrandr-dev libxtst-dev libxt-dev
sudo apt install -y libcups2-dev libfontconfig1-dev libasound2-dev

bash ./configure --with-debug-level=slowdebug --enable-ccache
make images
```

## issue

### runtime

#### InvalidKeyException: Illegal key size

Caused by: java.security.InvalidKeyException: Illegal key size
at javax.crypto.Cipher.checkCryptoPerm(Cipher.java:1039)

需升级jdk到比如 1.8.0_332
