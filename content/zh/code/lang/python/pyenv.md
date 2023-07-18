
---
title: "pyenv"
---

## install

```shell
curl https://pyenv.run | bash

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

## usage

**~/.pyenv/cache**

```shell
wget https://www.python.org/ftp/python/3.10.12/Python-3.10.12.tar.xz -P ~/.pyenv/cache/

export v=3.10.12; wget https://npm.taobao.org/mirrors/python/$v/Python-$v.tar.xz -P ~/.pyenv/cache/; pyenv install $v
```
