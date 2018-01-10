---
layout: post
keywords: Python, time, datetime
description: 收集关于 Python datetime 的经典学习资料，以便随时查阅
title: "Python 日期时间处理总结"
categories: [python]
tags: [python, time, datetime]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### Python 日期和时间处理

请参考以下经典的文献：

1. [PYTHON-基础-时间日期处理小结](http://www.wklken.me/posts/2015/03/03/python-base-datetime.html#1-datetime)

补充说明，将 `datetime` 转换成 `timestamp`：

```Python
import datetime as dt
now = dt.datetime.today()
now_timestamp = now.timestamp()
print(now_timestamp)
print(type(now_timestamp))
```




