---
layout: post
keywords: Python, logging
description: 收集关于 Python logging 的经典学习资料，以便随时查阅
title: "Python logging 参考资料"
categories: [python]
tags: [python, logging]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### Python Logging 入门参考资料

1. [Python Logging: An In-Depth Tutorial](https://www.toptal.com/python/in-depth-python-logging)
2. [Python之日志处理（logging模块）](https://www.cnblogs.com/yyds/p/6901864.html)
1. [Logging HOWTO](https://docs.python.org/3/howto/logging.html)
3. [Good logging practice in Python](https://fangpenlin.com/posts/2012/08/26/good-logging-practice-in-python/)
5. [Best way to log a Python exception](http://stackoverflow.com/questions/5191830/best-way-to-log-a-python-exception)
1. [Logging in Python](https://realpython.com/python-logging/)


### Python Logging Advanced 参考资料

1. [Logging Cookbook](https://docs.python.org/3/howto/logging-cookbook.html)
1. [Python Logging: A Stroll Through the Source Code](https://realpython.com/python-logging-source-code/)


### Python Logging 增强软件包

1. [mrfh (Multiprocess Rotating File Handler)](https://github.com/di/mrfh) is a drop-in replacement for the logging modules's RotatingFileHandler which provides a process-safe rotating log file handler using file-based locks.
1. [concurrent-log-handler](https://github.com/Preston-Landers/concurrent-log-handler) provides an additional log handler for Python's standard logging package. This handler will write log events to a log file which is rotated when the log file reaches a certain size. Multiple processes can safely write to the same log file concurrently.


### Python 第三方 Log 软件包

1. [eliot](https://github.com/itamarst/eliot) is the logging system that tells you *why* it happened.
1. [loguru](https://github.com/Delgan/loguru) is a library which aims to bring enjoyable logging in Python.
1. [structlog](https://github.com/hynek/structlog) makes logging in Python less painful and more powerful by adding structure to your log entries.
1. [logbook](https://github.com/getlogbook/logbook) is a cool logging replacement for Python.
