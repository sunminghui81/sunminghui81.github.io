---
layout: post
keywords: shell
description: 收集关于shell的经典学习资料，以便随时查阅
title: "收集shell常用命令"
categories: [tool]
tags: [shell]
group: archive
icon: code
---
{% include mathsyouth/setup %}


### shell常用命令

1. `!$` 是一个特殊的环境变量，它代表了上一个命令的最后一个字符串
1. `sudo !!` 以root的身份执行上一条命令。场景举例：比如Ubuntu里用apt-get安装软件包的时候是需要root身份的，我们经常会忘记在apt-get前加sudo。
1. 在shell下，有时候你会输入很长的命令，你可以使用`!xxx`来重复最近的一次命令，比如，你以前输入过，vim /where/the/file/is, 下次你可以使用`!vi` 重得上次最近一次的vi命令
1. `cd –` 回到上一次的目录
1. `^old^new` 替换前一条命令里的部分字符串。场景：echo "wanderful"，其实是想输出echo "wonderful"。只需要^a^o就行了，对很长的命令的错误拼写有很大的帮助。（陈皓注：也可以使用 !!:gs/old/new）
1. `du -s * | sort -n | tail` 列出当前目录里最大的10个文件
1. `> file.txt` 创建一个空文件
1. `mtr google.com`
1. 在命令行前加空格，该命令不会进入history里
1. `echo “ls -l” | at midnight` 在某个时间运行某个命令
1. `tail -f /path/to/file.log | sed '/^Finished: SUCCESS$/ q'` 当file.log里出现Finished: SUCCESS时候就退出tail，这个命令用于实时监控并过滤log是否出现了某条记录。
1. `ssh user@server bash < /path/to/local/script.sh` 在远程机器上运行一段脚本。这条命令最大的好处就是不用把脚本拷到远程机器上。
1. `ssh user@host cat /path/to/remotefile | diff /path/to/localfile –` 比较一个远程文件和一个本地文件
1. `vim scp://username@host//path/to/somefile` vim一个远程文件
1. `python -m SimpleHTTPServer` 一句话实现一个HTTP服务，把当前目录设为HTTP服务目录，可以通过http://localhost:8000访问 这也许是这个星球上最简单的HTTP服务器的实现了。
1. `history | awk '{CMD[$2]++;count++;} END { for (a in CMD )print CMD[a] " " CMD[a]/count*100 "% " a }' | grep -v "./" | column -c3 -s " " -t | sort -nr | nl | head -n10` 这行脚本能输出你最常用的十条命令，由此甚至可以洞察你是一个什么类型的程序员。
1. `Ctrl-R` 查找历史命令
1. `Ctrl-W` 来删除最后一个单词，使用 `Ctrl-U` 来删除一行
1. `pstree -p` 显示进程树
1. 使用`pgrep`和`pkill`来找到或是kill某个名字的进程。 (-f 选项很有用)
1. 临时切换shell, 如果用的是zsh，直接输入bash即可切换成bash
1. 进程管理3个命令：ps、kill、top。ps aux or ps -ef都是显示所有用户进程；kill PID根据进程号，直接终止进程；
1. tar命令
   * 创建一个归档文件 `tar -cvf test.tar test1/ test2/`
   * 列出归档文件中的内容 `tar -tf test.tar`
   * 解开归档文件 `tar -xvf test.tar`
   * 解压gz文件（非常常用） `tar -zxvf test.tar.gz`
1. 减少grub默认的引导时间 `sudo vim /etc/default/grub`修改TIMEOUT值，然后`sudo update-grub`生效
1. 在ubuntu安装的时候默认root用户是不开启的，需要建立一个非root用户 。ubuntu系统默认root用户是不能登录的，密码也是空的。 如果要使用root用户登录，必须先为root用户设置密码。打开终端，
输入：`sudo passwd root`然后按回车，此时会提示你输入密码，在password:后输入密码。
1. 更改用户密码：`sudo passwd username`，root用户可以修改其他用户的密码，但是普通账户只能修改自己的密码，即不带参数的passwd命令。
1. 创建用户命令两条：`adduser`, `useradd`; 用户删除命令`userdel`。`adduser`会自动为创建的用户指定主目录、系统shell版本，会在创建时输入用户密码；`useradd`需要使用参数选项指定上述基本设置，如果不使用任何参数，则创建的用户无密码、无主目录、没有指定shell版本。
1. [source, ., ./](http://askubuntu.com/questions/182012/is-there-a-difference-between-and-source-in-bash-after-all?lq=1)


### Linux入门

1. [LINUX/UNIX 新手和专家教程](http://coolshell.cn/articles/1042.html)
1. [Linux Shell Scripting Tutorial](https://bash.cyberciti.biz/guide/Main_Page)

### 参考文献

1. [你可能不知道的SHELL](http://coolshell.cn/articles/8619.html)
1. [应该知道的LINUX技巧](http://coolshell.cn/articles/8883.html)



