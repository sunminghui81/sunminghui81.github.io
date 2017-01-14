---
layout: post
keywords: oslo.config
description: oslo.config 的学习总结
title: "学习 oslo.config"
categories: [python]
tags: [OpenStack, oslo.config]
group: archive
icon: compass
---
{% include mathsyouth/setup %}

### 简介

oslo.config 是用于从命令行或配置文件解析配置参数的框架，主要特性包括：

* 参数的类型限定
* 同时管理命令行与配置文件(ini)
* 自动生成示例配置文件
* 支持参数分组
* 运行时重新载入配置

### 参考资料

1. [python 配置管理：oslo.config](https://blog.apporc.org/2016/08/python-%E9%85%8D%E7%BD%AE%E7%AE%A1%E7%90%86%EF%BC%9Aoslo-config/)
1. [argparse – Command line option and argument parsing](https://pymotw.com/2/argparse/)
1. [Argparse Tutorial](https://docs.python.org/2/howto/argparse.html)
