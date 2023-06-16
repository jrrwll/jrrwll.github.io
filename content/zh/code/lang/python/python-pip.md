
---
title: "Python Pip"
---

## install
```shell
wget https://bootstrap.pypa.io/get-pip.py -O - | python
```

## pip install

```shell
python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade pip
```

### Mac ARM

#### numpy
```shell
brew install openblas
OPENBLAS="$(brew --prefix openblas)" pip install numpy
```

## dep man

### pdm

```shell
pip install pdm
pdm self update
```

```shell
mkdir my-project && cd my-project
pdm init
# requires-python = ">=3.7,<3.11"
pdm config pypi.url https://pypi.tuna.tsinghua.edu.cn/simple

pdm add requests==2.25.1
pdm build
```
