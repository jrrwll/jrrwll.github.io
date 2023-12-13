
---
title: "transformers"
---

## download

### huggingface

https://huggingface.co

缓存目录 `~/.cache/huggingface/hub`

```shell
HF_ENDPOINT=https://hf-mirror.com
python your_script.py
```

### modelscope

https://modelscope.cn/models

```python
'''
pip install modelscope
'''

# 调用 ChatGLM3-6B 模型
from modelscope import AutoTokenizer, AutoModel, snapshot_download
model_dir = snapshot_download("ZhipuAI/chatglm3-6b", revision = "v1.0.0")
tokenizer = AutoTokenizer.from_pretrained(model_dir, trust_remote_code=True)
model = AutoModel.from_pretrained(model_dir, trust_remote_code=True).half().cuda()
model = model.eval()
```
