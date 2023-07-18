
---
title: "Python Pip"
---

## config

```shell
wget https://bootstrap.pypa.io/get-pip.py -O - | python

python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade pip
```

```shell
cat requirements.txt | cut -f1 -d"#" | sed '/^\s*$/d' | xargs -n 1 pip install
```

## install

### numpy

```shell
brew install openblas
OPENBLAS="$(brew --prefix openblas)" pip install numpy
```

### pyarraow

```shell
apt-get install libarrow-devel libarrow-python-devel
```

## pdm

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
