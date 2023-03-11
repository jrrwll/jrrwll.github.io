
---
title: "Python Pip"
---

## Install pip
```shell
wget https://bootstrap.pypa.io/get-pip.py -O - | python

```
## Install packages for Mac ARM

#### numpy
```shell
brew install openblas
OPENBLAS="$(brew --prefix openblas)" pip install numpy
```
