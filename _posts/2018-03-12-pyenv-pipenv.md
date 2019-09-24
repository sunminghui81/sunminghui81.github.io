---
layout: post
keywords: pyenv, pipenv, virtualenv, virtualenvwrapper
description: 收集关于 Python 版本和环境管理的经典学习资料，以便随时查阅
title: "Python 版本和虚拟环境管理工具"
categories: [python]
tags: [pyenv, pipenv, virtualenv, virtualenvwrapper]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### pyenv 和 pipenv

`pyenv` 是一个 Python 的版本管理工具，便于在不同的 Python 版本中进行切换，支持每个项目的 Python 版本管理。如果需要管理 Python 虚拟环境，需要使用 `pyenv-virtualenv` 插件。可以使用 `pyenv shell` 命令来设置当前 Shell session 下的 Python 版本。

`pipenv` 的目的是为了给 Python 提供最好的打包工具。当安装/删除安装包事，它可以自动创建和管理项目的虚拟环境，从 Pipfile 中添加/删除安装包。它也会产生重要的 `Pipfile.lock` 文件，它用来产生确定性的构建。关于 `Pipfile` 和 `setup.py` 的区别，可以参考 [Pipfile vs setup.py](https://docs.pipenv.org/en/latest/advanced/#pipfile-vs-setuppy)。


### MacOS 版本和虚拟环境管理工具

#### 在 MacOS 上安装 `pyenv` 和 `pipenv`

安装 `pyenv` 和 `pipenv`：
```
brew update
brew install pyenv
brew install pipenv
```

也可以用下节方式来管理虚拟环境。

#### 在 MacOS 上安装 `virtualenv` 和 `virtualenvwrapper`

详细过程请参考：[Install Python 2.7, virtualenv and virtualenvwrapper on OS X Mavericks/Yosemite](http://www.marinamele.com/2014/05/install-python-virtualenv-virtualenvwrapper-mavericks.html)。

### Ubuntu 16.04 安装 pyenv 和 pipenv

利用 `pyenv-installer` 工具安装 `pyenv`:
```
curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
```
`pyenv` 升级：
```
pyenv update
```

#### 安装指定版本的 Python

首先，系统系统需要安装：
```
sudo apt-get install -y build-essential zlib1g-dev libssl-dev
# 选择安装
sudo apt-get install libsqlite3-dev libbz2-dev libreadline-dev
```

列出可安装的 Python 版本：
```
pyenv install -l
```
安装 Python 3.6.4:
```
pyenv install 3.6.4
```
卸载 Python 3.6.4：
```
pyenv uninstall 3.6.4
```
设置 Shell 的 Python 版本：
```
pyenv shell 3.6.4
```
清除 Shell 的 Python 版本：
```
pyenv shell --unset 
```

#### 对虚拟环境进行操作

安装 `pyenv-virtualenv`:
```
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bash_profile
exec "$SHELL"
```

创建 virtualenv：
```
pyenv virtualenv 3.6.4 test
```
列表 virtualenv：
```
pyenv virtualenvs
```
激活 virtualenv：
```
pyenv activate test
```
禁用 virtualenv：
```
pyenv deactivate
```
删除 virtualenv：
```
pyenv uninstall -f test
```

#### 安装 `pipenv`

安装指定版本的 `pipenv`：
```
pyenv shell 3.6.4
pip install pipenv
```

查找所有的命令：
```
pipenv --help
```
