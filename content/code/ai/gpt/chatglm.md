
---
title: "ChatGLM3"
---

## install

https://github.com/THUDM/ChatGLM3

```python
from transformers import AutoTokenizer, AutoModel
tokenizer = AutoTokenizer.from_pretrained("THUDM/chatglm3-6b", trust_remote_code=True)
model = AutoModel.from_pretrained("THUDM/chatglm3-6b", trust_remote_code=True, device='cuda')
model = model.eval()

response, history = model.chat(tokenizer, "你好", history=[])
print(response)

response, history = model.chat(tokenizer, "晚上睡不着应该怎么办", history=history)
print(response)
```

```shell
# 启动基于 Streamlit 的网页版 demo
streamlit run web_demo2.py
# 启动命令行对话 Demo
python cli_demo.py
```
