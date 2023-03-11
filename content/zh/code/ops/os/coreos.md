
---
title: "CoreOS"
---

## rpm-ostree

```shell
# omz chsh
sudo rpm-ostree install git wget zsh vim util-linux-user
# compile
sudo rpm-ostree install make gcc g++ patch
# pkg
sudo rpm-ostree install dnf

sudo systemctl reboot
```

### python

```shell
curl https://pyenv.run | bash

# add to ~/.zshrc
cat <<EOF >> ~/.zshrc
export PYENV_ROOT="\$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="\$PYENV_ROOT/bin:\$PATH"
eval "\$(pyenv init -)"
EOF

# dep lib to compile
sudo rpm-ostree install readline-devel zlib-devel openssl-devel bzip2-devel sqlite-devel libffi-devel lzma-sdk-devel

pyenv versions
pyenv install 2.7.18
pyenv install 3.9.13
pyenv global 2.7.18

# pyarrow
sudo rpm-ostree install libarrow-devel libarrow-python-devel
```
