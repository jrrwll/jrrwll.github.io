
---
title: "Python - CP01E01"
date: 2018-10-17
---

## python

> 写于 2018-10-17 的一篇python教程

a python file like this:

```python
#!/usr/bin/env python3
#coding=utf-8

# it will print a poetry for python
import this

if __name__ == '__main__':
    print('Hello, World!')
```
at line 1: it will search a file named `python3` in your *$PATH* to execute

at line 2: `#coding=utf-8` or `# -*- coding: UTF-8 -*-`, will make python support your local characters.

at line 8: in python 2 version, it can replace with `print 'Hello, World!' `. In fact, *print*  is a function in pyhton3 but a command in python2.

You can save this to a file such as **awesome.py** , and type the command in terminal: `/usr/bin/env python3 awesome.py`.

### Declaration and assignment

```python
a = b = c = 1
assert a == 1 and b == c and a + b + c == 3

a, b, c = 1, 3.14, "a simple string"
```

### Operator

```python
import random

assert 1 + 1 - 1 * 1 + 1 / 1 == 2.0
# <float> = <int> / <int>
assert type(1 / 3) == float
assert 1 / 3 > 0.3333 and 1 / 3 < 0.3334
assert 6 / 10 == 3 / 5 

# <int> = <int> // <int>
assert 16 // 3 == 5 and 16 // 4 == 4 and 16 // 5 == 3
# <int> = <int> % <int>
assert 16 % 3 == 1 and 16 % 4 == 0 and 16 % 5 == 1

# 16 - 20
a = random.randint(16, 21)
# 2 - 7
b = random.randint(2, 8)
print("a = %d, b = %d" % (a, b))
assert a == b * (a // b) + (a % b)

# <number> ** <number>
assert 2 ** 4 == 16 and 2 ** 8 == 256
assert 2 ** 16 == 65536
assert 1.2 ** 2 == 1.44
```

### Data type

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-

# integer
ten_i = 10
# float
one_tenth_f = 1.0E-1
# string
hello_world = 'Hello' + " " + """
world!
"""

assert type(ten) == int
assert type(one_tenth_f) == float
assert type(hello_world) == str
```

### Data type: list, tuple and dict

```python
beatles = ["John", "Paul", "George", "Lingo"]

if '''Bob''' in beatles:
    print("You just kiding me!")
elif """Lennon""" in beatles:
    print('Right, but never print this!')
else:
    pass

for name in beatles:
    print("The member of the beatles: %s" % name)
  
assert beatles[0] == "John"
# change the member's value in the list
beatles[0] = "John Lennon"
assert beatles[0] == "John Lennon"
```

