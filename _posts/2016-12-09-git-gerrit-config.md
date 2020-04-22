---
layout: post
keywords: git，gerrit, 配置
description: Git 和 Gerrit 的一些常用配置
title: "Git 和 Gerrit 配置"
categories: [tool]
tags: [Git, Gerrit]
group: archive
icon: git
---
{% include mathsyouth/setup %}

### Git 配置

#### Git 配置全局用户名和邮箱

为所有的文件夹配置统一的用户名和邮箱：

配置用户名:

```shell
git config --global user.name "Firstname Lastname"
```

确认用户名配置成功:

```shell
git config --global user.name
```

配置邮箱:

```shell
git config --global user.email "your_email@youremail.com"
```

确认邮箱配置成功:

```shell
git config --global user.email
```

#### Git 为单个文件夹配置用户名和邮箱

为单个文件夹配置用户名和邮箱：

进入到当前文件夹的根目录下:

```shell
cd project/
```

配置用户名:

```shell
 git config user.name "Firstname Lastname"
```

确认用户名配置成功:

```shell
git config user.name
```

配置邮箱:

```shell
git config user.email "your_email@youremail.com"
```

确认邮箱配置成功:

```shell
git config user.email
```

#### Git 配置 editor

If you want to set the editor only for Git, do either (you don’t need both):

* Set `core.editor` in your Git config: `git config --global core.editor "vim"`
* Set the `GIT_EDITOR` environment variable: `export GIT_EDITOR=vim`

#### Git 配置代理

Git 配置全局代理：

```shell
git config --global http.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080
```
**请注意，需要查看自己的端口是不是也是1080，可以打开你的SS查看代理设置。** 同时，也请注意，这里指的是 http 和 https 协议:

```shell
git clone https://www.github.com/xxxx/xxxx.git
git clone http://www.github.com/xxxx/xxxx.git
```
对于SSH协议:

```shell
git clone git@github.com:xxxxxx/xxxxxx.git
```
依然无效。

不推荐直接用全局代理，如果挂了全局代理，需要克隆 coding 之类的国内仓库，会奇慢无比。 所以建议使用这条命令，只对 github 进行代理，对国内的仓库不影响:

```shell
git config --global http.https://github.com.proxy http://127.0.0.1:1080
git config --global https.https://github.com.proxy https://127.0.0.1:1080
```
同时，如果在输入这条命令之前，已经输入全局代理的话，可以输入以下命令进行取消

```shell
git config --global --unset http.proxy
git config --global --unset https.proxy
```

#### Git 其他配置

* 开启颜色显示: `git config --global color.ui true`


### Gerrit 配置

在Ubuntu上安装`git-review`:

```shell
apt-get install git-review
```

`git review` 配置文件夹:

```shell
cd <projectname>
git review -s
```

配置 Git 使用 Gerrit 的用户名:

```shell
git config gitreview.username yourgerritusername
```

如果要配置全局的用户名，可以:

```shell
git config --global gitreview.username yourgerritusername
```

### 更改commit的author

主要参考：

1. [invalid author](https://gerrit-review.googlesource.com/Documentation/error-invalid-author.html)
1. [Change commit author at one specific commit](http://stackoverflow.com/questions/3042437/change-commit-author-at-one-specific-commit)

For every pushed commit Gerrit verifies that the e-mail address of the author
matches one of the registered e-mail addresses of the pushing user.

If only the last commit is affected you can do this by amending the last commit
and explicitly setting the author:

```shell
git commit --amend --author "Firstname Lastname <youremail@example.com>"
```

Change the author of one specific commit in the history, but not last commit.
For example, if your commit history is `A-B-C-D-E-F` with `F` as `HEAD`, and
you want to change the author of `C` and `D`, then you would:

1. `git rebase -i B`
1. change the lines for both `C` and `D` to `edit`
1. Once the rebase started, it would first pause at `C`
1. `git commit --amend --author="Firstname Lastname <youremail@example.com>"`
1. `git rebase --continue`
1. It would pause again at `D`
1. `git commit --amend --author="Firstname Lastname <youremail@example.com>"`
1. `git rebase --continue`
1. The rebase would complete.

Here is an example that shows how the interactive rebase is used to update the
author for the last 3 commits:

```shell
git rebase -i HEAD~3
git commit --amend --author "Firstname Lastname <youremail@example.com>"
git rebase --continue
```

### 参考资料

1. [git clone一个github上的仓库，太慢，经常连接失败，但是github官网流畅访问，为什么？](https://www.zhihu.com/question/27159393)
1. [Getting Git to work with a proxy server - fails with “Request timed out”](https://stackoverflow.com/questions/783811/getting-git-to-work-with-a-proxy-server-fails-with-request-timed-out)

