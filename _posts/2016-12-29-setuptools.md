---
layout: post
keywords: setuptools
description: setuptools是经典的Python包安装工具
title: "学习setuptools"
categories: [python]
tags: [setuptools]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### [setup.py简介][setuptools]

*setuptools* 是一款便于开发者构建和分发Python包的工具，它基于Python工具 *distutils* 进行的增强开发。

#### 基本使用

运用 *setuptools* 进行安装的典型脚本 *setup.py* 如下：

```python
from setuptools import setup, find_packages
setup(
    name="HelloWorld",
    version="0.0.1",
    packages=find_packages(),
    scripts=['say_hello.py'],

    # Project uses reStructuredText, so ensure that the docutils get
    # installed or upgraded on the target machine
    install_requires=['docutils>=0.3'],

    package_data={
        # If any package contains *.txt or *.rst files, include them:
        '': ['*.txt', '*.rst'],
        # And include any *.msg files found in the 'hello' package, too:
        'hello': ['*.msg'],
    },

    data_files=[('bitmaps', ['bm/b1.gif', 'bm/b2.gif']),
                ('config', ['cfg/data.cfg']),
                ('/etc/init.d', ['init-script'])],

    # metadata for upload to PyPI
    author="Me",
    author_email="me@example.com",
    description="This is an Example Package",
    license="PSF",
    keywords="hello world example examples",
    url="http://example.com/HelloWorld/",  # project homepage, if any
    classifiers=[
        'Development Status :: 3 - Alpha',
        'License :: OSI Approved :: MIT License',
        'Programming Language :: Python :: 2.7',
        'Topic :: Text Processing :: Linguistic',
    ],

    # could also include long_description, download_url, classifiers, etc.

    # other arguments here...
    entry_points={
        'console_scripts': [
            'foo = my_package.some_module:main_func',
            'bar = other_module:some_func',
        ],
        'gui_scripts': [
            'baz = my_package_gui:start_func',
        ]
    }
)
```

接下来，对`setup()`的参数进行解释说明：

1. `name`项目的名称，它决定着该项目在 *PyPI* 中的名字。
1. `version`指定了项目的版本号，选择版本控制方案可参考 [Choosing a versioning scheme](https://packaging.python.org/distributing/#choosing-a-versioning-scheme)；
1. `install_requires`用于指定项目能够运行所需要的最小依赖项。当项目通过pip安装时，这是用于安装其依赖项的规范。当你的项目通过使用EasyInstall，setup.py install或setup.py develop安转时，所有尚未安装的依赖包将被定位（通过PyPI），下载，构建（如果需要）和安装。
1. `package_data`参数是一个从包名称到glob模式列表的字典。如果数据文件包含在包的子目录中，则glob可以包括子目录名称。比如：`package_data={'mypkg': ['data/*.dat'],}`。
1. `data_files`可用于指定模块分发所需的其他文件：配置文件，消息目录，数据文件。它指定了一系列 *(目的安装目录，源文件)* 对，如果　*目的安装目录*　是相对路径，则相对于安装前缀进行解释；*源文件* 中的每个文件名都将相对于包源分发包顶部的setup.py脚本进行解释, 来自 *源文件* 的目录信息不用于确定已安装文件的最终位置。 　
1. `packages`：　若`value`选择`find_packages()`，需要一个源目录和两个用来排除和包含包的包名称模式列表；如果参数省略，源目录默认为`setup.py`所在的目录。
1. `scripts`安装pre-made脚本，同`entry_points`类似，推荐使用后者。
1. `entry_points`关键字可以用来指定你的项目为任何命名入口点所提供的任意插件，这些入口点可能由你的项目或者你依赖的其他项目来定义。运用`console_scripts` entry points来注册脚本的接口，然后你可以让工具链将这些接口转换为实际的脚本。在上面的示例中，创建了两个控制台脚本foo和bar，以及名为baz的GUI脚本。当这个项目安装在非Windows平台上时（使用"setup.py install”，“setup.py develop”或使用EasyInstall），将安装一组foo，bar和baz脚本，这组脚本从指定的模块中导入函数main_func和some_func。这些函数调用没有参数。

### 项目安装

#### 从PyPI上安装

安装最新版本

```
pip install 'some_project'
```

安装特定版本

```
pip install 'some_project==1.4'
```

从PyPI上更新已经安装的包

```
pip install --upgrade some_project
```

#### 从Git上安装

```
pip install -e git+https://git.repo/some_pkg.git#egg=some_project
```

#### 安装Ｗheels格式分发包

首先安装wheel

```
pip install wheel
```

接下来, 安装wheel打包文件

```
pip install /<path>/some_project.whl
```

#### 安装源码分发包

```
pip install /<path>/some_project-1.0.4.tar.gz
```

#### 以开发模式安装源码

在正常情况下，distutils假定你会构建项目的分发，而不是以“原始”或“未构建”的形式来使用它。如果你这样使用distutils，每次你在开发过程中改变它时你必须重建和重新安装你的项目。setuptools允许部署项目以在公共目录或暂存区域中使用，但不复制文件。每次更新源代码后，使用`setup.py develop`命令，除了不安装任何东西外，它和命令`setup.py install`或`EasyInstall`工具相似。它会在部署文件下创建一个特殊的`.egg-link`文件，该文件会链接到你项目的源代码。

如果从你的项目中删除了部分源代码或数据文件，通过运行命令`setup.py clean --all`删除过时的`.pyc`或`.pyo`文件。完成给定的开发任务后，可以使用`setup.py develop --uninstall`命令将项目源代码从暂存区域中删除，如果不是默认值，则指定所需的暂存区。

pip的安装命令可以使用-e选项，用来从本地代码目录或者版本库URL来安装一个开发版本的库。采用这种方式的时候，在安装目录下只会创建一个包含软件包信息的文件，真正的代码不会安装到系统目录下：

```shell
pip install -e <path>
```

`-e`是`--editable`的缩写，`<path>`表示源码路径，注意，依赖包的安装模式并不是 *editable*。想在可编辑模式下安装一些依赖项也很常见。 例如，假设你的项目需要“foo”和“bar”，但是你希望从vcs中以可编辑模式来安装“bar”，那么你可以构建一个需求文件 *requirements.txt*，如：

```
-e <path>
-e git+https://somerepo/bar.git#egg=bar
```

如果不想安装依赖包，可以运行命令：

```
pip install -e <path> --no-deps
```

### 需求文件requirements.txt

为了方便参数`install_requires`可配置, 可以创建需求文件"requirements.txt", 它包含了一系列需要安装的依赖包，关于`install_requires`和"requirements.txt"的区别可以参考 [requirements file](https://packaging.python.org/requirements/)。安装依赖包，运行命令：

```python
pip install -r requirements.txt
```

### 项目打包

要使项目可以从像PyPI的软件包索引进行安装，需要创建项目的一个Distribution。

#### 源码分发

要创建源码发布，源代码需要打包到单个归档文件中, 这可以使用sdist命令完成：

```shell
python setup.py sdist
```

“源码分发”不是built distribution，并且当由pip安装时需要built步骤。即使distribution是纯python的（即不包含扩展），它仍然需要一个built步骤从setup.py中构建安装元数据。执行`sdist`命令时会在你的项目下创建 *dist* 子文件夹，并生成格式为`.tar.gz`（在POSIX系统下）或`.zip`（在Windows下）的压缩文件，默认会打包以下内容：

1. `packages`中指定的源码文件
1. `ext_modules`或`libraries`指定的C源码文件
1. 由`scripts`指定的脚本文件
1. anything that looks like a test script: test/test*.py
1. Top level files named: README.txt, README, setup.py, or setup.cfg
1. `package_data`或`data_files`指定的文件

#### Wheels

wheel是一个可以安装而不需要通过built过程的构建包，对于最终用户而言, 安装wheel比从源码分发安装要快得多。

如果你的项目是纯python（即不包含编译的扩展）和本地支持Python 2和3，需要创建"universal wheel":

```shell
python setup.py bdist_wheel --universal
```

还可以在“setup.cfg”中永久设置--universal标志

```
[bdist_wheel]
universal=1
```

如果你的项目是纯python（即不包含编译的扩展），但本地不同时支持Python 2和3，需要创建"pure python wheel":

```shell
python setup.py bdist_wheel
```

`bdist_wheel` will detect that the code is pure Python, and build a wheel that’s named such that it’s usable on any Python installation with the same major version (Python 2 or Python 3) as the version you used to build the wheel.

如果你的项目是不包含编译的扩展，需要创建"platform wheel":

```shell
python setup.py bdist_wheel
```

`bdist_wheel` will detect that the code is not pure Python, and build a wheel that’s named such that it’s only usable on the platform that it was built on.

### 参考

1. [Setuptools Documentation Release 32.3.0][setuptools]
1. [Python Packaging User Guide][PyPUG]
1. [pip][pip]
1. https://pypi.python.org/pypi?%3Aaction=list_classifiers
1. [The Hitchhiker’s Guide to Packaging][Hitchhiker]


[setuptools]: https://media.readthedocs.org/pdf/setuptools/latest/setuptools.pdf
[PyPUG]: https://packaging.python.org/
[pip]: https://pip.pypa.io/en/latest/
[Hitchhiker]: http://the-hitchhikers-guide-to-packaging.readthedocs.io/en/latest/index.html