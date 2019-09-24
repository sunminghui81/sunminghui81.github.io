---
layout: post
keywords: bash
description: Shell 脚本的不同执行方式会产生不同的影响
title: Shell 脚本的不同执行方式
categories: [unix-based]
tags: [shell]
group: archive
icon: linux
---
{% include mathsyouth/setup %}


### Shell 脚本的不同执行方式

Shell 脚本一共有 5 中不同的执行方式：

```shell
bash test.sh
sh test.sh
./test.sh
source test.sh
. test.sh
```
其中，前三种都是开启新的子 Shell 进程，后两种是在当前 Shell 进程中执行。假定， `test.sh`中的代码为：

```shell
#!/bin/bash
# demo
cd /tmp
pwd
```
那么前三种命令运行的结果就是当前 Shell 进程不会进入到 `/tmp` 目录，而后两种命令会切换到 `/tmp` 目录。此外，`source test.sh` 和  `. test.sh` 完全等价，`source` 命令的简写即是 `.`。下面主要解释前三种命令之间的细微差异。


#### 什么是 `sh` 和 `bash`

`sh` 是一个由 `POSIX` 标准所规定的编程语言，它有很多种实现，包括但不限于 `ksh88`、`dash` 和 `bash` 等。由于 `sh` 是一种规范而不是一种实现，`/bin/sh` 是某种实现的一个 symlink 或 hard link。`bash` 最初是和 `sh` 规范兼容的实现，但是随着时间的推移，它添加了很多和 `POSIX` 不兼容的扩展。很长时间内，`/bin/sh` 在大部分 GNU/Linux 系统上是指向 `/bin/bash`。因此，几乎可以认为它们之间没有什么差异。但是，最近发生了一些变化，比如，最新的 Debian 和Ubuntu 系统默认将 `/bin/sh` 指向 `/bin/dash`。所以 `bash test.sh` 和 `sh test.sh` 的区别就是后一种不一定是 `bash`。

如何确定自己的电脑上 `/bin/sh` 指向那种实现？ `/bin/sh` 可能是 symbolic link 或者 hard link。如果它是 symbolic link，那么可以通过如下命令查询：

```shell
$ file -h /bin/sh
/bin/sh: symbolic link to dash
```

如果是 hard link，可以尝试如下命令查询：

```shell
$ find -L /bin -samefile /bin/sh
/bin/sh.distrib
/bin/dash
/bin/sh
```
事实上，`-L` 包含了 symbolic link 和  hard link 两种，但是 `POSIX` 并不要求 `find` 命令支持 `-samefile`选项。

运行 `./test.sh` 会报错：

```
-bash: ./test.sh: Permission denied
```
运行一下命令即可：

```shell
chmod u+x test.sh
```


#### 参考资料

1. [Difference between sh and bash](https://stackoverflow.com/questions/5725296/difference-between-sh-and-bash/5725402#5725402)
