---
layout: post
keywords: packages, offline-installation
description: Python 包离线安装笔记
title: "Python 包离线安装入门"
categories: [python]
tags: [packages, offline-installation]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### Python 包离线安装

#### 下载并压缩 Python 包

指定需要安装的依赖包并创建 requirements.txt:

```
sasl>=0.2.1
thrift>=0.11.0
thrift_sasl>=0.3.0
future>=0.17.1
SQLAlchemy>=1.3.8
mock>=3.0.5
PyHive>=0.6.1
```

下载依赖包到指定的目录:

```shell
pip download \
  -r requirements.txt \
  --no-binary MarkupSafe,simplejson \
  -d /path/to/download/dir/
```

将 `requirements.txt` 复制到文件夹 `/path/to/download/dir/` 中，并压缩成包：

```shell
tar -zcf dir.tar.gz dir
```

#### 离线安装 Python 包

上传压缩包 `dir.tar.gz` 到服务器并解压：

```shell
tar -zxf dir.tar.gz 
```

离线安装：

```shell
pip install -r /path/to/download/dir/requirements.txt --no-index --find-links file:///path/to/download/dir/
```
或者
```shell
pip install -r /path/to/download/dir/requirements.txt --no-index -f path/to/download/dir/
```

其中，`--no-index` 表示忽略 PyPI package index，并查找 `--find-links` 提供的下载路径。 `-f, --find-links <URL>`  If a URL or path to an html file, then parse for links to archives. If a local path or file:// URL that's a directory, then look for archives in the directory listing.


### 参考资料

1. [How to install packages using pip according to the requirements.txt file from a local directory?](https://stackoverflow.com/questions/7225900/how-to-install-packages-using-pip-according-to-the-requirements-txt-file-from-a)
2. [Python Packages Offline Installation](https://stackoverflow.com/questions/11091623/python-packages-offline-installation) 

