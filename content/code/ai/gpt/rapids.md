
---
title: "Rapids AI"
---

## intro

RAPIDS 全称是Real-time Acceleration Platform for Integrated Data Science，是 NVIDIA 针对数据科学和机器学习推出的 GPU 加速库

## spark-rapids

https://github.com/nvidia/spark-rapids-ml

https://github.com/NVIDIA/spark-rapids-ml/blob/main/python/README.md

### install

```shell
pip install cudf-cu11 dask-cudf-cu11 --extra-index-url=https://pypi.nvidia.com
pip install cuml-cu11 --extra-index-url=https://pypi.nvidia.com
pip install cugraph-cu11 --extra-index-url=https://pypi.nvidia.com
```

```python
# verify
import cudf
print(cudf.Series([1, 2, 3]))
```
