---
layout: post
keywords: bash
description: 收集关于 bash 的经典学习资料，以便随时查阅
title: "Bash 常用命令"
categories: [unix-based]
tags: [shell]
group: archive
icon: code
---
{% include mathsyouth/setup %}


### 学会使用命令帮助

* 在只记得部分命令关键字的场合，可通过 `man -k` 来搜索: `man -k py`。
* 需要知道某个命令的简要说明，可以使用 `whatis`: `whatis python`, `whatis ls`。
* 详细的介绍，则可用 `info`: `info python`, `info ls`。
* 查看程序的 binary 文件所在路径，可使用 `which`: `which python`, `which ls`。
* 当系统中安装了同一软件的多个版本时，不确定使用的是哪个版本时，使用 `whereis` 命令就能查看程序的搜索路径: `whereis python`。
* 对于命令的具体参数及使用方法，需要用到强大的 `man`: `man python`, `man ls`。关于 `which` 和 `whereis` 的区别可以使用命令： `man which`, `man whereis`。


### 移动光标

* `Ctrl` + `A`：移到行首（`A` 是首字母）
* `Ctrl` + `E`：移到行尾（end）
* `Ctrl` + `B`：前移一个字符(backward)
* `Ctrl` + `F`：后移一个字符(forward)
* `Alt` + `B`：前移一个单词
* `Alt` + `F`: 后移一个单词
* `Alt` + `L`: 后移单词尾
* `Ctrl` + `XX`：在命令行首和光标之间移动

**Note: 对于 Ubuntu 14.04 来说，`Alt` + `F` 默认用作“启用菜单访问键”，若要此快捷键生效，需要修改默认配置。打开终端，点击 edit -> keyboard shortcuts, 然后去勾第一个选项 "Enable menu access keys (Such as `Alt` + `F` to open the File menu)"。或者用　`Alt` + `Super` + `F` 替代 `Alt` + `F`，此处 `Super` 键特指 win 键。**

### 编辑命令

* `Ctrl` + `U`：从光标处删除至命令行首
* `Ctrl` + `K`：从光标处删除至命令行尾
* `Ctrl` + `W`：从光标处删除至字首 (word)
* `Alt` + `D`：从光标处删除至字尾 (delete)
* `Alt` + `.`: 粘帖最后一次命令最后的参数（通常用于 mkdir long-long-dir 后, cd 配合着 `Alt` + `.`）
* `Ctrl` + `Y`：粘贴至光标后
* `Ctrl` + `Shift` + `C`: 复制
* `Ctrl` + `Shift` + `V`: 粘贴

### 重新执行命令

* 在命令行前加空格，该命令不会进入 `history` 里
* `history | awk '{CMD[$2]++;count++;} END { for (a in CMD )print CMD[a] " " CMD[a]/count*100 "% " a }' | grep -v "./" | column -c3 -s " " -t | sort -nr | nl | head -n10` 这行脚本能输出你最常用的十条命令，由此甚至可以洞察你是一个什么类型的程序员。
* `Ctrl` + `R`：逆向搜索命令历史
* `Ctrl` + `G`：从历史搜索模式退出
* `Ctrl` + `P`：历史中的上一条命令
* `Ctrl` + `N`：历史中的下一条命令

### Bang (!) 命令

* `!!`：执行上一条命令
* `!XXX`：执行最近的以 XXX 开头的命令，如 `!vim`
* `!XXX:p`：仅打印输出，而不执行
* `!$`：上一条命令的最后一个参数，与 `Alt` + . 相同
* `!$:p`：打印输出 `!$` 的内容
* `!*`：上一条命令的所有参数
* `!*:p`：打印输出 `!*` 的内容
* `sudo !!`： 以 root 的身份执行上一条命令。场景举例：比如 Ubuntu 里用 apt-get 安装软件包的时候是需要 root 身份的，我们经常会忘记在 apt-get 前加 sudo。
* ^blah：删除上一条命令中的 blah
* ^blah^foo：替换前一条命令里的部分字符串。场景： `echo "wanderful"`，其实是想输出 `echo "wonderful"`。只需要 `^a^o` 就行了，对很长的命令的错误拼写有很大的帮助。（陈皓注：也可以使用 `!!:gs/blah/foo`）
* ^blah^foo^：将上一条命令中所有的 blah 都替换为 foo

### 文件及目录管理

1. 查询目录： `ls [选项][文件或目录]`，选项
   * `-a` 所有文件
   * `-l` 查看详情
1. 建立目录： `mkdir -p [目录名]`, `-p` 表述递归建文件夹。举例： `mkdir -p work/code`
1. 切换目录： `cd [目录名]`，简化操作：
   * `cd ~` 进入当前用户目录
   * `cd -` 回到上一次的目录
   * `cd ..` 进入上一级目录
   * `pwd` 查看当前目录所在位置
1. 删除目录所有文件: `rm -r [目录名]`。举例： `rm -r work`；`rm /some/path/{file1,file2}`； `rm /some/path/file{1,2}`。
1. 复制目录: `cp[选项][原文件目录][目标目录]`，　选项：
   * `-r` 复制目录
1. 剪切、改名: `mv[原文件目录][目标文件目录]`
1. 常见目录作用
   * `/` 根目录
   * `/bin` 命令保存目录
   * `/boot` 启动目录
   * `/dev` 设备文件命令
   * `/etc` 配置文件保存目录
   * `/lib` 系统库保存命令
   * `/mnt` 系统挂载目录
1. 创建一个空文件:
   * `:> file.txt`
   * `touch file.txt`
1. 列出当前目录里最大的10个文件: `du -s * | sort -n | tail`
1. 批量下载网站的所有文件，比如：`http://clgiles.ist.psu.edu/IST597/materials/slides/`，运行命令
 `wget --random-wait -e robots=off -r -np -nH --cut-dirs=3 -R *index* http://clgiles.ist.psu.edu/IST597/materials/slides/`，其中：
   * `--random-wait` 为了减轻服务器的负担，在连续多次抓取的过程中随机等待；
   * `-e robots=off` `wget` 绕开 `robots.txt` 的规则；
   * `-r` 表示递归下载；
   * `-np` 表示不会下载上级文件 `/materials`；
   * `-nH` 表示下载时去掉文件名 `clgiles.ist.psu.edu`；
   * `--cut-dirs=3` 表示下载时去掉文件名 `IST597/materials/slides/`；
   * `-R *index*` 表示不会下载命名包含 `index` 的文件；
   * `-a pdf` 表示只下载 `pdf` 文件。


### 文件搜索命令

1. 文件搜索: `find [搜索范围][选项][条件]`。举例：
   * `find /path/ -name hello.sh` 在目录下查找名为 `hello.sh` 文件
   * `find /path/ -iname hello.sh` 忽略大小写查找文件
   * `find /path/ -iname "*.log"` 查找命名为 `xxx.log` 的文件
   * `find /var/log -mtime +10` `-mtime` 文件修改时间, `-atime` 文件访问时间, `-ctime` 改变文件属性时间; `+10` 10天前, `10`  10天, `-10` 10天内
   * `find /etc -size +20M` 查找文件大于20M的文件

### 压缩与解压缩命令

常见压缩格式：`.zip` 、 `.gz` 、`.bz2`、`.tar.gz` 、 `.tar.bz2`

1. `tar` 命令
   * 创建一个归档文件： `tar -cvf test.tar test1/ test2/`
   * 列出归档文件中的内容： `tar -tf test.tar`
   * 解开归档文件： `tar -xvf test.tar`
   * 解压 `.tar.gz` 文件（非常常用）： `tar -zxvf test.tar.gz`
   * 打包 `.tar.gz`: `tar -zcvf 压缩包名.tar.gz  原文件`
1. `.zip` 格式
   * 压缩文件： `zip [压缩文件名] [原文件]`
   * 压缩目录： `zip -r  [压缩文件名] [原文件]`
   * 解压缩： `unzip [压缩文件名]`
1. `.gz` 格式
   * 压缩为 `.gz` 格式，原文件不保留: `gzip [原文件]`
   * 压缩为 `.gz` 格式，原文件保留: `gzip -c [原文件] > [压缩文件]`
   * 压缩目录: `gzip -r  [目录]`
   * 解压缩: `gunzip [文件]`, `gunzip -r [目录]`

### 网络工具

1. 查看当前 IP 地址： `ifconfig eth0 |awk '/inet/ {split($2,x,":");print x[2]}'`
1. 参考文档[网络工具](http://linuxtools-rst.readthedocs.io/zh_CN/latest/base/07_network.html)
1. 查看路由列表: `netstat -rn` 或 `route -n`
1. 查看某域名与自己的电脑的网络状态: `ping google.com`
1. `mtr google.com`
2. 检查本机端口是否打开：
   * `nc -v 127.0.0.1 8000`
   * `telnet 127.0.0.1 8000`
1. 检查远程端口是否打开：
   * 运行命令 `nc -v google.com 80` 会显示：

     Connection to google.com 80 port [tcp/http] succeeded!    
     ^C  
   * 运行命令 `telnet google.com 80` 会显示：

     Trying 172.217.24.46...  
     Connected to google.com.  
     Escape character is '^]'. #==> ``Ctrl``+ `]`  
     ^]  
     telnet> quit  
     Connection closed.

### 远程操作

1. `ssh user@host bash < /path/to/local/script.sh` 在远程机器上运行一段脚本。这条命令最大的好处就是不用把脚本拷到远程机器上。
1. `ssh user@host cat /path/to/remotefile | diff /path/to/localfile –` 比较一个远程文件和一个本地文件
1. `vim scp://user@host//path/to/somefile` vim一个远程文件

### 用户管理

1. 在 Ubuntu 安装的时候默认 root 用户是不开启的，需要建立一个非 root 用户 。Ubuntu 系统默认 root 用户是不能登录的，密码也是空的。 如果要使用 root 用户登录，必须先为 root 用户设置密码。打开终端， 输入： `sudo passwd root` 然后按回车，此时会提示你输入密码，在 password: 后输入密码。
1. 更改用户密码： `sudo passwd username`，root 用户可以修改其他用户的密码，但是普通账户只能修改自己的密码，即不带参数的 passwd 命令。
1. 创建用户命令两条： `adduser`, `useradd`; 用户删除命令 `userdel`。`adduser` 会自动为创建的用户指定主目录、系统 shell 版本，会在创建时输入用户密码；`useradd` 需要使用参数选项指定上述基本设置，如果不使用任何参数，则创建的用户无密码、无主目录、没有指定 shell 版本。
1. 查看所有用户: `cat /etc/passwd`。若第三个参数500以上，表示新建用户，否则为系统用户；所有新建用户的目录放在 `/home` 下。

### 进程管理

1. [lsof](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/lsof.html) 是一个查看当前系统文件的工具。在 Linux 环境下，任何事物都以文件的形式存在，通过文件不仅仅可以访问常规数据，还可以访问网络连接和硬件。如传输控制协议 (TCP) 和用户数据报协议 (UDP) 套接字等，系统在后台都为该应用程序分配了一个文件描述符，该文件描述符提供了大量关于这个应用程序本身的信息。
2. [ps](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/ps.html) 命令用来列出系统中当前运行的那些进程。ps 命令列出的是当前那些进程的快照，就是执行 ps 命令的那个时刻的那些进程，如果想要动态的显示进程信息，就可以使用 top 命令。
3. [top](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/top.html) 命令是 Linux 下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况，类似于 Windows 的任务管理器。
   1. `top` 然后按 `O` 输入 `rsize` 查看每个进程的内存使用情况;
1. 清除僵尸进程： `ps -eal | awk '{ if ($2 == "Z") {print $4}}' | xargs sudo kill -9`

#### 查看进程
```shell
ps -ef
ps -aux
```

把 `ps` 查询结果通过管道给 `grep`查找包含特定字符串的进程。管道符“|”用来隔开两个命令，管道符左边命令的输出会作为管道符右边命令的输入：
```shell
ps -ef | grep pyenv
```
`pgrep` 的 `p` 表明了这个命令是专门用于进程查询的 `grep`：
```shell
pgrep pyenv
```

#### 杀死进程

```shell
kill -s 9 1827
```
`-s 9` 指定了传递给进程的信号是`９`，即强制、尽快终止进程。

#### 管道

```shell
pgrep pyenv | xargs kill -s 9
kill -s 9 `pgrep pyenv`
```

`xargs kill -s 9` 中的 `xargs` 命令是用来把前面命令的输出结果（PID）作为 `kill -s 9`命令的参数，并执行该命令。

### 后台运行

```shell
nohup flask run -h 0.0.0.0 > logs 2>&1 & 
tail -f logs
```
`nohup` 不挂断地运行命令，它是  `no hangup` 的缩写，意即“不挂断”。`&` 是指在后台运行。用 	`nohup` 运行命令可以使命令永久的执行下去，和用户终端没有关系，例如我们断开 SSH 连接都不会影响他的运行，注意 `nohup` 没有后台运行的意思；`&` 是指在后台运行，但当用户推出(挂起)的时候，命令自动也跟着退出。`1` 表示 stdout 标准输出，`2` 表示 stderr 标准错误，`2>&1` 把标准错误重定向到标准输出，是`&1` 而不是 `1`，这里`&1` 相当于等效于标准输出。


### Linux 查看物理 CPU个数、核数、逻辑 CPU 个数

总核数 = 物理CPU个数 X 每颗物理CPU的核数 
总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

查看物理CPU个数
```shell
cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l
```

查看每个物理CPU中core的个数 (即核数)
```shell
cat /proc/cpuinfo| grep "cpu cores"| uniq
```

查看逻辑CPU的个数
```shell
cat /proc/cpuinfo| grep "processor"| wc -l
```

查看CPU信息（型号）
```shell
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
```

### Mac 查看物理 CPU 核数、逻辑 CPU 个数

查看 CPU 信息
```shell
sysctl machdep.cpu
```

查看 CPU 核数
```shell
sysctl -n machdep.cpu.core_count
```

查看逻辑 CPU 个数
```shell
sysctl -n machdep.cpu.thread_count
```

### 其他常用工具和命令

1. `echo “ls -l” | at midnight` 在某个时间运行某个命令。
1. `tail -f /path/to/file.log | sed '/^Finished: SUCCESS$/ q'` 当 file.log 里出现 Finished: SUCCESS 时候就退出 tail，这个命令用于实时监控并过滤 log 是否出现了某条记录。
1. 临时切换 shell, 如果用的是 zsh，直接输入 bash 即可切换成 bash。
1. 减少 grub 默认的引导时间 `sudo vim /etc/default/grub` 修改 TIMEOUT 值，然后 `sudo update-grub` 生效。
1. 区别 [source, ., ./](http://askubuntu.com/questions/182012/is-there-a-difference-between-and-source-in-bash-after-all?lq=1)
1. 查看 GPU 信息： `lspci -vnn | grep VGA -A 12`。
2. 查看各文件夹大小 `du -h --max-depth=1 /path`。


### Linux 入门

1. [LINUX/UNIX 新手和专家教程](http://coolshell.cn/articles/1042.html)
1. [Linux Shell Scripting Tutorial](https://bash.cyberciti.biz/guide/Main_Page)
2. [Ubuntu 命令技巧](http://wiki.ubuntu.org.cn/UbuntuSkills)

### 参考文献

1. [你可能不知道的 SHELL](http://coolshell.cn/articles/8619.html)
1. [应该知道的 LINUX 技巧](http://coolshell.cn/articles/8883.html)
1. [Linux 基础之常用命令篇](http://www.jianshu.com/p/0718b3abedcf)
1. [让你提升命令行效率的 Bash 快捷键](https://linuxtoy.org/archives/bash-shortcuts.html)
1. [Bash Shell 常用快捷键](https://github.com/hokein/Wiki/wiki/Bash-Shell%E5%B8%B8%E7%94%A8%E5%BF%AB%E6%8D%B7%E9%94%AE)
1. [Linux 工具快速教程](http://linuxtools-rst.readthedocs.io/zh_CN/latest/index.html)
