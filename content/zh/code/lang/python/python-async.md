
---
title: "Async Python"
---

## gunicorn
< A Python WSGI HTTP server, run on Unix-like OS, inspired by ruby unicorn
< pre-fork-worker模式，一个master进程管理多个worker进程
< 推荐的worker数量是：(2 * $num_cores) + 1

```shell
pip install gunicorn greenlet eventlet gevent

# -k, --worker-class 工作模式
gunicorn -k sync --workers=17 --threads 1 --worker-connections 1000

```
### sync 多进程模式
一次仅处理一个请求

### eventlet, gevent 协程模式
协程实现（cooperative multi-threading），利用非同步IO让一个process在等待IO回应时继续处理下个请求

### gthread 多线程模式
线程工作模式，利用线程池管理连接

### gaiohttp
利用aiohttp库实现异步I/O

