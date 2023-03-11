
---
title: "OpenJDK"
---

## compile openjdk

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
