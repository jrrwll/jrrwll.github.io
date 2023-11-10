
---
title: "async-profiler"
---

```shell
# --event cpu, --event alloc
# frame_buffer_overflow: --framebuf 5000000 (default is 1000000)
# --include 'java/*' --include 'demo/*' --exclude '*Unsafe.park*'
# --duration 300 自动5min之后结束
profiler start
profiler actions
profiler status
profiler getSamples
profiler stop --format html --file some.html
# cpu alloc lock itimer
profiler list
# http://localhost:3658/arthas-output/
```

## arthas

```shell
./as.sh
```
