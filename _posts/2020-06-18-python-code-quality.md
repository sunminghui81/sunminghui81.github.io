---
layout: post
keywords: pdb, debugging, black, flake8, pylint, type-checking, mypy, python-test, pytest 
description: 提高 Python 代码质量
title: "提高 Python 代码质量入门资料汇总"
categories: [python]
tags: [pdb, debugging, black, flake8, pylint, type-checking, mypy, python-test, pytest]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### Python debugging 参考资料

#### Python debugging 入门文献

1. [pdb-tutorial](https://github.com/spiside/pdb-tutorial) is a simple tutorial about effectively using pdb.
2. [Python Debugging With Pdb](https://realpython.com/python-debugging-pdb/)
3. [Advanced Python Debugging with pdb](https://www.codementor.io/@stevek/advanced-python-debugging-with-pdb-g56gvmpfa)
1. [pudb使用指南](http://legendtkl.com/2015/10/31/pudb-howto/)
1. [pudb’s documentation](https://documen.tician.de/pudb/index.html)

#### Python debugging 视频教程

1. [Introduction to PDB](https://www.youtube.com/watch?v=vfPtGsSJldg)
1. [In Depth PDB](https://www.youtube.com/watch?v=lnlZGhnULn4)


### Python format and code quality tool

1. [Python Code Quality: Tools & Best Practices](https://realpython.com/python-code-quality/)
1. [black](https://github.com/psf/black) formats Python code without compromise.
1. [isort](https://github.com/timothycrosley/isort) formats imports by sorting alphabetically and separating into sections.
1. [interrogate](https://github.com/econchick/interrogate) checks your code base for missing docstrings.
1. [flake8](https://flake8.pycqa.org/en/latest/) is capable of detecting both logical and stylistic lint. It adds the style and complexity checks of pycodestyle to the logical lint detection of PyFlakes.
1. [pylint](https://www.pylint.org/) checks for errors, tries to enforce a coding standard, looks for code smells.


### Type checking

1. [Python Type Checking (Guide)](https://realpython.com/python-type-checking/)
1. [Mypy documentation](https://mypy.readthedocs.io/en/stable/index.html)
1. [pyre-check](https://github.com/facebook/pyre-check)
1. [pytype](https://github.com/google/pytype)
1. [typeshed](https://github.com/python/typeshed)
1. [pyannotate](https://github.com/dropbox/pyannotate) insert annotations into your source code based on call arguments and return types observed at runtime.
1. [MonkeyType](https://github.com/Instagram/MonkeyType) is a system for Python that generates static type annotations by collecting runtime types.
1. [pydantic](https://github.com/samuelcolvin/pydantic/) enforces type hints at runtime, and provides user friendly errors when data is invalid.


### Python test 入门资料

1. [Getting Started With Testing in Python](https://realpython.com/python-testing/)
1. [Effective Python Testing With Pytest](https://realpython.com/pytest-python-testing/)
1. [pytest-watch](https://github.com/joeyespo/pytest-watch) is a zero-config CLI tool that runs pytest, and re-runs it when a file in your project changes.
1. [pytest-cov](https://pytest-cov.readthedocs.io/en/latest/) is a coverage plugin for pytest.
1. [Continuous Integration With Python: An Introduction](https://realpython.com/python-continuous-integration/)


#### pytest 实用技巧

如果没有 `setup.py` 文件，同时假定 `tests` 文件夹和 package 文件夹在当前目录下，可以运行 `python -m pytest tests` 来直接测试 package 中的代码。

