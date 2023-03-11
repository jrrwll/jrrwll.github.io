
---
title: "Python Test"
---

## unittest

```python
import unittest

class TestStringMethods(unittest.TestCase):

    def test_somecase(self):
        self.assertEqual('foo'.upper(), 'FOO')
        self.assertTrue('FOO'.isupper())
        self.assertFalse('Foo'.isupper())

if __name__ == '__main__':
    unittest.main()
```

```shell
python -m unittest test_module1 test_module2
python -m unittest test_module.TestClass
python -m unittest test_module.TestClass.test_method

python -m unittest -v tests/test_something.py
```
