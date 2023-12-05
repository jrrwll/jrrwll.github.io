
---
title: "ChatGLM3"
---

## install

https://github.com/THUDM/ChatGLM3

```python
# 设置 huggingface.co 镜像
# export HF_ENDPOINT=https://hf-mirror.com

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
cd basic_demo
# 启动基于 Gradio 的网页版 demo
pip install mdtex2html
# model = AutoModel.from_pretrained(MODEL_PATH, trust_remote_code=True).quantize(4).to(DEVICE).eval()
python web_demo.py

# 启动基于 Streamlit 的网页版 demo
streamlit run web_demo2.py
```

```shell
# 启动命令行对话 Demo
python cli_demo.py
```

**在显存较小的场景**

```python
model = AutoModel.from_pretrained("THUDM/chatglm3-6b",trust_remote_code=True).quantize(4).cuda()
```