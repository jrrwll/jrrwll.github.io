
---
title: "pyenv"
---

## install

```shell
curl https://pyenv.run | bash
# or in china mainland
set -e
curl -ksSL https://raw.fastgit.org/pyenv/pyenv-installer/master/bin/pyenv-installer | sed 's/https:\/\/github.com/https:\/\/gitclone.com\/github.com/' | bash
# or just
git clone https://gitclone.com/github.com/pyenv/pyenv.git ~/.pyenv
git clone https://gitee.com/mirrors/pyenv ~/.pyenv

# add to ~/.zshrc
cat <<EOF >> ~/.zshrc
export PYENV_ROOT="\$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="\$PYENV_ROOT/bin:\$PATH"
eval "\$(pyenv init -)"
EOF
```

**compile dependency**

```shell
sudo apt-get install readline-devel zlib-devel openssl-devel bzip2-devel sqlite-devel libffi-devel lzma-sdk-devel -y

pyenv versions
pyenv install 2.7.18
pyenv global 2.7.18
```

```shell
sudo apt-get install g++ make -y
sudo apt-get install zlib1g-dev libreadline-dev libssl-dev libbz2-dev libsqlite3-dev libffi-dev liblzma-dev -y
```

## usage

**~/.pyenv/cache**

```shell
wget https://www.python.org/ftp/python/3.10.13/Python-3.10.13.tar.xz -P ~/.pyenv/cache/

export v=3.10.13; wget https://npm.taobao.org/mirrors/python/$v/Python-$v.tar.xz -P ~/.pyenv/cache/; pyenv install $v

export v=3.10.13; wget https://npm.taobao.org/mirrors/python/$v/Python-$v.tgz -P ~/.pyenv/cache/Python-$v.tar.gz; pyenv install $v
```

## pyenv-virtualenv

```shell
# or for mac: brew install pyenv-virtualenv
git clone https://github.com/pyenv/pyenv-virtualenv.git ~/.pyenv/plugins/pyenv-virtualenv
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.zshrc
```

```shell
pyenv virtualenv 3.6.5 v365env
pyenv activate v365env
pyenv deactivate v365env
```
