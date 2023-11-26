
---
title: "Stable Diffusion"
---

## stable-diffusion-webui

### prepare python environment

```shell
# brew install xz
apt-get install liblzma-dev -y
# in stable-diffusion-webui
venv/bin/pip install backports.lzma
```

**修改 lib/python3.10/lzma.py**

```python
# 修改前
from _lzma import *
from _lzma import _encode_filter_properties, _decode_filter_properties

# 修改后
try:
from _lzma import *
from _lzma import _encode_filter_properties, _decode_filter_properties
except ImportError:
from backports.lzma import *
from backports.lzma import _encode_filter_properties, _decode_filter_properties
```

### prepare GPU

https://developer.nvidia.com/cuda-toolkit-archive

```shell
# use gpu, --medvram for 4g, --lowvram for 2g
# or use cpu(very slowly), pass: --use-cpu sd
nvcc --version
```

### start

```shell
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
cd stable-diffusion-webui
sh ./webui.sh
```

**modify webui-user.sh, add: --no-half**

```shell
export COMMANDLINE_ARGS="--skip-torch-cuda-test --upcast-sampling --no-half --no-half-vae --use-cpu interrogate"
```
