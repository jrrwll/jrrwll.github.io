
---
title: "Python Std"
---

## base

### datetime & time
```python
import datetime
import time

dt = datetime.datetime.now()
unix_sec = time.mktime(dt.timetuple())
dt = datetime.datetime.fromtimestamp(time.time())

s = dt.strftime("%Y-%m-%d %H:%M:%S")
dt = datetime.datetime.strptime(s, "%Y-%m-%d %H:%M:%S")
unix_sec = time.mktime(time.strptime(s, "%Y-%m-%d %H:%M:%S"))
s = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime(unix_sec))
```
### random
```python
import random

n_float = random.uniform(0, 10) # [0, 10)
n_float = random.random() # [0, 1.0)

# random.randrange([start], stop[, step])
n_int = random.randint(1, 3) # [1, 3]

s_str = random.choice(['r', 'g', 'b'])
s_list = random.sample(['r', 'g', 'b'], 2)
```

## file

### ConfigParser
```python
import ConfigParser
conf = ConfigParser.ConfigParser()
conf.read("myapp.ini")
sections = conf.sections()
section = sections [0]
keys = conf.options("sec")
kvs = conf.items("sec")
val = conf.get("sec", "key")
int_val = conf.getint("sec", "key")
```