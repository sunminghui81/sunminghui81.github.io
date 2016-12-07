---
layout: post
keywords: SystemExit, Exception, except
description: 理解Python中的SystemExit异常
title: "SystemExit异常处理"
categories: [python]
tags: [SystemExit, Exception]
group: archive
icon: compass
---
{% include mathsyouth/setup %}

下面catch SystemExit的方式会失败：

```python
try:
    raise SystemExit
except Exception as exception:
    print "success"
```

但是

```python
try:
    raise SystemExit
except:
    print "success"
```

可以成功。原因是[SystemExit does not inherit from Exception][SystemExit]， 但是可以使用
except BaseException来catch SystemExit：

```python
try:
    raise SystemExit
except　BaseException:
    print "success"
```

[Exception hierarchy]给出了exeption类之间的继承关系，关于SystemExit的一些建议，可以参考
[Python sys.exit: a suggestion].

[SystemExit]: https://docs.python.org/3/library/exceptions.html#SystemExit
[Exception hierarchy]: https://docs.python.org/3/library/exceptions.html#exception-hierarchy
[Python sys.exit: a suggestion]: http://blog.frank-mich.com/python-sys-exit-a-suggestion/