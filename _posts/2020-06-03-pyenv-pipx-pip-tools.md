---
layout: post
keywords: pyenv, pipenv, virtualenv, virtualenvwrapper, pipx
description: 收集关于 Python 版本和环境管理的经典学习资料，以便随时查阅
title: "Python 版本和虚拟环境管理工具"
categories: [python]
tags: [pyenv, pipenv, virtualenv, virtualenvwrapper, pipx]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### pyenv、pipenv 和 pipx

[`pyenv`](https://github.com/pyenv/pyenv) 是一个 Python 的版本管理工具，便于在不同的 Python 版本中进行切换，支持每个项目的 Python 版本管理。如果需要管理 Python 虚拟环境，需要使用 [`pyenv-virtualenv`](https://github.com/pyenv/pyenv-virtualenv) 插件。可以使用 `pyenv shell` 命令来设置当前 Shell session 下的 Python 版本。

[`pipenv`](https://github.com/pypa/pipenv) 的目的是为了给 Python 提供最好的打包工具。当安装/删除安装包事，它可以自动创建和管理项目的虚拟环境，从 Pipfile 中添加/删除安装包。它也会产生重要的 `Pipfile.lock` 文件，它用来产生确定性的构建。关于 `Pipfile` 和 `setup.py` 的区别，可以参考 [Pipfile vs setup.py](https://docs.pipenv.org/en/latest/advanced/#pipfile-vs-setuppy)。

[`pipx`](https://github.com/pipxproject/pipx/) 是一款全局安装 Python 包的工具。


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

如果需要对 pyenv 进行升级，需要运行命令：

```shell
brew update && brew upgrade pyenv
```

升级之后，可能会报以下类似的错误：

```
/Users/linghui/.pyenv/shims/python3.7: line 21: /usr/local/Cellar/pyenv/1.2.19/libexec/pyenv: No such file or directory
```

解决方法是运行命令：

```shell
pyenv rehash
```

详细说明可以参考[pyenv puts incorrect path in shims with Homebrew](https://github.com/pyenv/pyenv/issues/816)。

### Ubuntu 16.04 安装 pyenv

利用 `pyenv-installer` 工具安装 `pyenv`:
```
curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
```
`pyenv` 升级：
```
pyenv update
```

安装系统依赖包：

```
sudo apt-get install -y build-essential zlib1g-dev libssl-dev
# 选择安装，最好安装，否则会报 Warning
sudo apt-get install libsqlite3-dev libbz2-dev libreadline-dev
```

### 安装指定版本的 Python

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

#### 加速下载 Python 安装包

如果下载压缩包时（如：https://bitbucket.org/pypy/pypy/downloads/pypy3.6-v7.3.1-osx64.tar.bz2）比较慢，可以利用浏览器走代理下载，操作步骤如下：

如果不存在 `~/.pyenv/cache` 缓存目录，则创建
```shell
mkdir -p ~/.pyenv/cache
```

利用浏览器下载压缩包，并将压缩包放在上述缓存目录中，然后再次安装就可以了（如：`pyenv install pypy3.6-7.3.1`）。
最后，手动清空上述缓存目录:
```shell
rm -rf ~/.pyenv/cache/*
```


### 切换Python 版本

设置全局的 Python 版本，通过将版本号写入到 `~/.pyenv/version` 文件中：
```
pyenv global 3.8.2
```

设置 Python 本地版本，通过将版本号写入当前目录下的 `.python-version` 文件中：
```
pyenv local 3.8.2
```
pyenv 会从当前目录开始向上逐级查找 `.python-version` 文件，直到根目录为止。若找不到，就用全局版本。

设置 Shell 的 Python 版本：
```
pyenv shell 3.8.2
```
清除 Shell 的 Python 版本：
```
pyenv shell --unset 
```

寻找 Python 运行环境的优先级:
```
shell > local > global
```


### 对虚拟环境进行操作

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

### 安装 `pipenv` and `pipx`

安装 `pipenv`:
```
pyenv shell 3.8.2
python -m pip install pipenv
```

安装 `pipx`
```
pyenv shell 3.8.2
python -m pip install --user pipx
python -m pipx ensurepath
```

全局安装 `black`、`flake8` 和 `pylint`:
```
pipx install black
pipx install flake8
pipx install pylint
```


### Python 其他包管理工具

1. [pip-tools](https://github.com/jazzband/pip-tools) is a set of tools to keep your pinned Python dependencies fresh.
1. [poetry](https://github.com/python-poetry/poetry) helps you declare, manage and install dependencies of Python projects, ensuring you have the right stack everywhere.


### Python virtual environment 参考资料

1. [Python Virtual Environments: A Primer](https://realpython.com/python-virtual-environments-a-primer/)
1. [A quick-and-dirty guide on how to install packages for Python](https://snarky.ca/a-quick-and-dirty-guide-on-how-to-install-packages-for-python/)
1. [Install Python 2.7, virtualenv and virtualenvwrapper on OS X Mavericks/Yosemite](http://www.marinamele.com/2014/05/install-python-virtualenv-virtualenvwrapper-mavericks.html)
1. [There’s no magic: virtualenv edition](https://www.recurse.com/blog/14-there-is-no-magic-virtualenv-edition)


### Python 包管理工具参考资料

1. [Why you should use `python -m pip`](https://snarky.ca/why-you-should-use-python-m-pip/)
1. [Managing Multiple Python Versions With pyenv](https://realpython.com/intro-to-pyenv/)
1. [Pipenv: promises a lot, delivers very little](https://chriswarrick.com/blog/2018/07/17/pipenv-promises-a-lot-delivers-very-little/)
1. [不要用 Pipenv](https://zhuanlan.zhihu.com/p/80478490)
1. [Create and Publish a Python Package with Poetry](https://johnfraney.ca/posts/2019/05/28/create-publish-python-package-poetry/)
1. [Tame the Snake: Python Package Management with Pyenv, Pipenv & Pipx](https://jacobsgill.es/python-package-management)
1. [Overview of python dependency management tools](https://modelpredict.com/python-dependency-management-tools)
1. [Python Application Dependency Management in 2018](https://hynek.me/articles/python-app-deps-2018/)
