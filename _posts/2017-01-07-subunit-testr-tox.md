---
layout: post
keywords: test, subunit, testrepository, tox
description: 学习 OpenStack 测试框架
title: "subunit, testrepository 和 tox 学习笔记"
categories: [python]
tags: [test, subunit, testr, tox]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### [测试用例运行和管理工具 subunit 简介][subunit]

subunit 是一个用于传输单元测试结果的流协议。一般来说，运行单元测试的时候是把单元测试的结果直接输出到标准输出，但是如果运行大量的测试用例，这些测试结果就很难被分析。因此可以使用 python-subunit 模块来运行测试用例, 使用它可以很容易地实现以下功能:

1. 测试聚合：单独运行的测试可以组合，然后一起报告/显示。 例如，来自不同语言的测试可以被示为无缝整体，并且在多个机器上运行的测试可以通过复用器聚合成单个流。
1. 测试存档：运行的测试可以被录制下来，以便今后可以被重放。
1. 测试隔离：可能会崩溃或以其他方式与彼此交互不良的测试可以单独运行，然后聚合，而不是彼此干扰或需要一个特别的 test - >runner 报告协议。
1. 网格测试：subunit 可以充当必要的序列化和反序列化，以便在分布式机器上实时报告测试运行的结果。

#### 创建 subunit 结果流

最常见的方式是运行现有的 python 测试套件，并通过 subunit.run 模块使其输出 subunit:

```shell
python -m subunit.run test_module
```

#### 使用 subunit 结果流

##### 转换成不同的格式

如果要将 subunit 转换为其他标准格式，可能已有转换工具。 例如，可以通过 subunit2junixml 工具再次从 subunit 中获得 junitxml：

```shell
python -m subunit.run test_module | subunit2junitxml
```

也可以运行测试用例：

```shell
python -m subunit.run test_module | subunit2pyunit
```

subunit2pyunit 命令会解析 subunit 协议，并且输出到标准输出。

##### 提取元数据

如果只是想得到一个存在于 subunit 流中的所有测试列表，你可以通过 subunit-ls 管道化这个流:

```shell
python -m subunit.run test_module | subunit-ls
```

这将所有的测试 ids 打印到 stdout，每行一个。 类似地，我们可以通过使用 subunit-stats 工具从 subunit 流中提取聚合统计信息:

```shell
python -m subunit.run discover test_suite | subunit-stats
```

如果要运行由多个 test_module 文件组成的文件夹 test_suite，应该加上参数 discover。

总之，subunit 是一种现代化的测试结果格式，它可以充分利用由 testtools 等工具包提供的现代化测试特性。特别地，对于验收测试套件，能够将任意内容附加到测试结果，并且使该数据跟随测试结果。它可以用单个文件来包含测试作者认为重要的所有信息。这些数据可以在机器之间移动，并随意解压。

### [测试用例运行和管理工具 testrepository 简介][testr]

Test repository 是一个用于跟踪测试结果的小应用程序, 可以表示为 subunit stream 的任何测试运行能够被插入到一个 repository 中。典型的工作流是具有插入测试运行的 repository，然后查询 repository 以找出需要处理的问题。testr 可以自动化这一工作流，它通过使用测试运行器列出所有的测试来工作，它获得这个列表并将其分配到与当前机器上可用的 CPU 数量匹配的多个分区，然后 fork 这些测试运行器给每个分区一份测试列表。最后，测试运行器将 subunit 的结果回传到 testr，以便 tesr可以跟踪测试成功和失败以及其他统计数据。总之，testr 将并行运行测试（所以它们更快），并保持结果的鲁棒日志。

testr 使用 `python-subunit` 模块来运行测试用例，然后分析 subunit 的输出并对测试结果进行记录（记录到本地文件）。举例来说，testr 允许你做这样的事情：

1. 知道哪些用例运行时间最长
1. 显示运行失败的用例
1. 重新运行上次运行失败的用例

testr 通过 `.testr.conf` 文件进行配置，该文件需要在运行 testr 的同一目录中。

下面以 OpenStack Tempest 中的配置文件 `.testr.conf` 为例来学习 testr 的使用。

```
[DEFAULT]
# 运行以执行测试的命令行
test_command=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
             OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
             OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
             OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
             ${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover} $LISTOPT $IDOPTION
# 当运行特定测试 ids 时，将该值代入到 test_command 中
test_id_option=--load-list $IDFILE
# 通过查询测试程序来看哪些测试将运行。运行 `testr list-tests` 命令将会列出所有的测试用例
test_list_option=--list
# 将特定类型的测试分组在一起，以便它们被同一后端运行
group_regex=([^\.]*\.)*
```

### [测试环境管理工具 tox 简介][tox]

tox 是一个通用的虚拟环境管理和测试命令行工具。对于一个项目，我们需要运行 Python 2.7 的单元
测试，也需要运行 Python 3.4 的单元测试，还需要运行 PEP8 的代码检查。这些不同的任务需要依赖
不同的库，所以需要使用不同的虚拟环境。使用 tox 的时候，我们会在 tox 的配置文件`tox.ini`中
指定不同任务的虚拟环境名称，该任务在虚拟环境中需要安装哪些包，以及该任务执行的时候需要运行哪
些命令。我们除了可以使用 tox 来管理虚拟环境，还可以调用 testr 来执行测试用例。总之，tox 使
用步骤如下：

1. 安装 tox `pip install tox`；
1. 生成 tox 配置文件，在其中指定 virtualenv 列表以及每个 virtualenv 环境下要运行的测试
命令；
1. 由 tox 命令自动创建准备 virtualenv，并在其中运行指定的测试命令。

下面以 OpenStack Tempest 中的配置文件 `tox.ini` 为例来学习 tox 的使用。

```
# content of: tox.ini , put in same dir as setup.py
# tox 支持使用一个额外的 [tox:jenkins] 参数来替换 [tox] 中指定的内容。当检测到是在
# jenkins 中调用 tox 时，tox 自动使用 [tox:jenkins] 中的配置，而不是 [tox]
[tox]
# envlist 表示本文件中包含的配置环境，defaults to the list of all environments
# 确定 tox 操作的环境列表，以此顺序发生
envlist = pep8,py35,py34,py27,pip-check-reqs
minversion = 2.3.1
# 默认情况下，tox 会对当前项目执行 `python setup.py install` 以安装到 virtualenv 中，
# 这包含了 `python setup.py sdist` 打包操作，对于大一些的项目，这会是一个费时的过程。当
# 我们在调试代码并频繁执行单元测试的时候，我们不会想要每次都跑一遍打包操作。
skipsdist = True

# 将默认值放在 [tempestenv] 中，并在其他节中引用它们，以避免重复相同的值
[tempestenv]
# default: False, meaning that virtualenvs will be created without inheriting
# the global site packages.
sitepackages = False
setenv =
    VIRTUAL_ENV={envdir}
    OS_TEST_PATH=./tempest/test_discover
deps =
    setuptools
    -r{toxinidir}/requirements.txt

# testenv 是默认配置，如果某个配置在环境专属的 section 中没有，就从这个 section 中读取
[testenv]
# 如果需要设置像 PYTHONPATH 这样的环境变量，可以使用 setenv 指令
setenv =
    VIRTUAL_ENV={envdir}
    OS_TEST_PATH=./tempest/tests
    PYTHONWARNINGS=default::DeprecationWarning
# 默认情况下，tox 只会将 PATH 环境变量从 tox 调用传递到测试环境。如果你想传递额外的环境变
# 量，可以使用 passenv 选项
passenv = OS_STDOUT_CAPTURE OS_STDERR_CAPTURE OS_TEST_TIMEOUT OS_TEST_LOCK_PATH OS_TEST_PATH TEMPEST_CONFIG TEMPEST_CONFIG_DIR http_proxy HTTP_PROXY https_proxy HTTPS_PROXY no_proxy NO_PROXY
# usedevelop 表示安装 virtualenv 的时候，本项目自己的代码采用开发模式安装
# `python setup.py develop`，也就是不会拷贝代码到 virtualenv 目录中，只是做个链接。
usedevelop = True
# install_command 设置用于将软件包安装到虚拟环境中，包括被测包以及任何定义的依赖。
install_command = pip install -U {opts} {packages}
# 默认情况下，在 virtualenv 中不能使用外部安装的命令，这本来是为了命令环境的隔离，但有些情
# 况下，我们会想要使用外部命令。例如，在对代码对格式检查时，我们不会想要在每个 virtualenv
# 中都安装一遍 flake8，只需调用外部环境中唯一的一份 flake8 即可；或有时你可能想使用工具
# make 或 bash。为了避免出现警告，可以使用 whitelist_externals 配置:
whitelist_externals = *
# deps 指定构建环境的时候需要安装的依赖包；
# 这些依赖包在项目包安装之前安装到环境中,每行定义一个依赖关系，它将被传递给 installer 命令
# 进行处理。
# {toxinidir} (the directory where tox.ini resides)
deps =
    -r{toxinidir}/requirements.txt
    -r{toxinidir}/test-requirements.txt
# commands 表示构建好 virtualenv 之后要执行的命令
commands =
    find . -type f -name "*.pyc" -delete
    ostestr {posargs}

# 这个 section 是为 genconfig 环境定制 commands 配置，它将覆盖掉 [testenv] 中的
# commands 配置项，告诉 tox 不重复使用基础 [testenv] 配置中的 commands 设置； 对于没有
# 定制的配置，使用 [testenv]　中的默认配置。 例如 deps，由于在 [testenv:genconfig] 中
# 未指定，所以沿袭 [testenv] 中的值。
[testenv:genconfig]
commands = oslo-config-generator --config-file tempest/cmd/config-generator.tempest.conf

[testenv:cover]
setenv = OS_TEST_PATH=./tempest/tests
commands = python setup.py testr --coverage --testr-arg='tempest\.tests {posargs}'

[testenv:all]
# 用户可以设置环境的特定路径。如果路径不是绝对的，它将被视为相对于 {toxinidir}。
# 默认：{toxworkdir}/{envname}, {toxworkdir} 创建虚拟环境的目录和用于打包的子目录
envdir = .tox/tempest
# section 的值可以通过 {[sectionname]valuename} 方式引用，这样可以避免配置参数的重复
sitepackages = {[tempestenv]sitepackages}
# 'all' includes slow tests
setenv =
    {[tempestenv]setenv}
# 该值通过 os.environ ['OS_TEST_TIMEOUT'] 的返回值获取，如果环境变量不存在，则替换为1200
    OS_TEST_TIMEOUT={env:OS_TEST_TIMEOUT:1200}
deps = {[tempestenv]deps}
commands =
    find . -type f -name "*.pyc" -delete
    tempest run --regex {posargs}
```

在代码根目录执行 `tox` 命令就可以发起测试，测试的过程中， tox 会首先在 .tox 目录下分别建
立 virtualenv，并在其中安装 `deps` 选项指定的依赖包，最后使用 `commands` 选项指定的命令
发起测试。通过

```
tox -e ENV [，ENV2，...]
```

可以运行 virtualenv `ENV [，ENV2，...]` 环境下的测试，比如：

```
tox -e genconfig
```

tox 命令首先会读取 `tox.ini` 文件中的 `[testenv:genconfig]` 配置信息，然后根据配置构建 virtualenv，保存在 .tox/ 目录下，以 `genconfig` 命名, 最后将在 `genconfig` 环境中调用 `commands` 命令。如果配置了 `endir`，则 virtualenv 会保存在 `endir` 下。

如果你像这样调用 tox：

```
tox -e all -- tempest.api.compute.servers.test_servers_negative.ServersNegativeTestJSON.test_reboot_non_existent_server
```

`--` 后面的参数将替换你在测试命令中指定的 `{posargs}`。

利用 tox 执行 Tempest 的顺序如下：

1. "user" execute “tox” command from terminal.
1. "tox" load configuration from “tox.ini”, create virtual environment and invoke testr.
1. "testr" load configuration from “.testr.conf”, invoke testrunner “subunit.run”.
1. "subunit.run" discover all the test cases (cases that extended testtools), execute test cases。


### 参考文档

1. [subunit官方文档][subunit]
1. [subunit源代码](https://github.com/testing-cabal/subunit/tree/master/python/subunit)
1. [Making the most of Subunit](http://www.tech-foo.net/making-the-most-of-subunit.html)
1. [testrepository官方文档][testr]
1. [OpenStack Testr](https://wiki.openstack.org/wiki/Testr)
1. [tox官方文档][tox]
1. [OpenStack Testing](http://developer.openstack.org/sdks/python/openstacksdk/contributors/testing.html)
1. [通过demo学习OpenStack开发——单元测试](http://www.infoq.com/cn/articles/the-development-of-openstack-unit-test)
1. [Tempest Deep Dive](http://lingxiankong.github.io/blog/2015/05/22/tempest-deep-dive/)
1. [python 持续集成： tox 单元测试](https://blog.apporc.org/2016/08/python-%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E5%B7%A5%E5%85%B7-tox/)

[subunit]: https://pypi.python.org/pypi/python-subunit/
[testr]: http://testrepository.readthedocs.io/en/latest/
[tox]: https://tox.readthedocs.io/en/latest/