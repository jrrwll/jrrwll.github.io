
---
title: "Install Python"
---

## pyenv

```shell
curl https://pyenv.run | bash

pyenv install --list
pyenv versions
pyenv global 3.7.15
```

#### install python2.7
```shell
sudo apt install make gcc g++ patch cmake -y

sudo apt install libssl-dev libbz2-dev libreadline-dev zlib1g.dev libsqlite3-dev libffi-dev lzma-dev libsnappy-dev libjpeg-dev default-libmysqlclient-dev -y
pyenv install 2.7.18
```

## build from source

```shell
wget http://www.python.org/ftp/python/3.7.15/Python-3.7.15.tgz
tar -zxvf Python-3.7.15.tgz
cd Python-3.7.15

./configure --prefix=/opt/python3 --enable-optimizations
make
make install
make clean
make distclean

/opt/python3/bin/python3 -V
```

## python2

### debian9

```shell
# docker pull debian:9
# docker run -d -it --name debian9 debian:9
# https://mirrors.tuna.tsinghua.edu.cn/help/debian/
apt update 
apt install python python-pip -y
pip install -U setuptools
```

```shell
apt install make gcc g++ patch cmake -y
apt install libssl-dev libbz2-dev libreadline-dev zlib1g.dev libsqlite3-dev libffi-dev lzma-dev libsnappy-dev libjpeg-dev default-libmysqlclient-dev -y
```

```shell
# mysql-python
# for old debian: libmysqlclient-dev
apt install default-libmysqlclient-dev -y
pip install mysql-python

# pyarrow
apt install -y -V ca-certificates lsb-release wget
wget https://apache.jfrog.io/artifactory/arrow/$(lsb_release --id --short | tr 'A-Z' 'a-z')/apache-arrow-apt-source-latest-$(lsb_release --codename --short).deb -O /tmp/apache-arrow.deb
apt -y install /tmp/apache-arrow.deb
apt -y update
apt -y install libarrow-dev libarrow-python-dev
rm /tmp/apache-arrow.deb
```

### fedora

```shell
# docker pull fedora
# docker run -d -it --name fedora fedora
# https://mirrors.tuna.tsinghua.edu.cn/help/fedora/

# python2 is not included
# dnf whatprovides pip
# sudo alternatives --set python /usr/bin/python2

dnf update && dnf install git curl -y
curl https://pyenv.run | bash
cat <<EOF >> ~/.bashrc
export PYENV_ROOT="\$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="\$PYENV_ROOT/bin:\$PATH"
eval "\$(pyenv init -)"
EOF
source ~/.bashrc

dnf install make gcc g++ -y
dnf install readline-devel zlib-devel openssl-devel bzip2-devel sqlite-devel libffi-devel lzma-sdk-devel -y
pyenv install 2.7.18
pyenv global 2.7.18
```

```shell
# pyarrow
dnf install cmake libarrow-devel libarrow-python-devel -y
pip install arrow pyarrow

# mysql-python
dnf install mysql-devel -y
pip install mysql-python
```
