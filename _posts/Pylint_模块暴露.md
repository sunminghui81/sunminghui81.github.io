---
layout: post
keywords: Python
description: Python lint 错误信息 2
title: Python Lint 错误 2
categories: [Python]
tags: [安全编码]
group: archive
icon: google
---

### E0603 undefined_all_variable
python中，当使用```from module import *```语句时，会导入这个module的所有非下划线开头的成语；
这样的操作存在本模块的命名空间被污染的问题。__all__的申明操作，可以在使用```from module import *```导入模块时，指定需要导入的成员。

```python
test1.py and test2.py in the same package
-----------------
test1.py
__all__ = ['func1']

def func1():
    print("func1")


def func2():
    print("func2")


def _func3():
    print("func3")

----------------
test2.py
from test1 import *

func1()

# if test1.__all__ set
# NameError: name 'func2' is not defined
# func2()

# NameError: name 'func3' is not defined
# func3()  
```

### E0604 invalid_all_object
在上面E0603的基础上__all__的申明对象，必须是string类型的。其它类型的对象不支持。
```python
__all__ = (
  1, # [invalid-all-object]
  lambda: None, # [invalid-all-object]
  None, # [invalid-all-object]
)
```



