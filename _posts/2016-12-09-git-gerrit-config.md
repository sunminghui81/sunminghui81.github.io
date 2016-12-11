---
layout: post
keywords: git，gerrit, 配置
description: Git和Gerrit的一些常用配置
title: "Git和Gerrit配置"
categories: [tool]
tags: [git, gerrit]
group: archive
icon: git
---
{% include mathsyouth/setup %}

### Git配置

#### Git配置全局用户名和邮箱

为所有的文件夹配置统一的用户名和邮箱：

1. 配置用户名
```Shell
git config --global user.name "Firstname Lastname"
```
1. 确认用户名配置成功
```Shell
git config --global user.name
```
1. 配置邮箱
```Shell
git config --global user.email "your_email@youremail.com"
```
1. 确认邮箱配置成功
```Shell
git config --global user.email
```

#### Git为单个文件夹配置用户名和邮箱

为单个文件夹配置用户名和邮箱：

1. 进入到当前文件夹的根目录下
```Shell
cd project/
```
1. 配置用户名
```Shell
 git config user.name "Firstname Lastname"
```
1. 确认用户名配置成功
```Shell
git config user.name
```
1. 配置邮箱
```Shell
git config user.email "your_email@youremail.com"
```
1. 确认邮箱配置成功
```Shell
git config user.email
```

### Gerrit配置

1. 在Ubuntu上安装`git-review`
```Shell
apt-get install git-review
```

1. `git-review`配置文件夹
```Shell
cd <projectname>
git review -s
```

1. 配置Git使用Gerrit的用户名
```Shell
git config gitreview.username yourgerritusername
```
如果要配置全局的用户名，可以
```Shell
git config --global gitreview.username yourgerritusername
```

### 更改commit的author
主要参考：
1. [invalid author](https://gerrit-review.googlesource.com/Documentation/error-invalid-author.html)
2. [Change commit author at one specific commit](http://stackoverflow.com/questions/3042437/change-commit-author-at-one-specific-commit)

For every pushed commit Gerrit verifies that the e-mail address of the author
matches one of the registered e-mail addresses of the pushing user.

If only the last commit is affected you can do this by amending the last commit
and explicitly setting the author:
```Shell
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
```Shell
git rebase -i HEAD~3
git commit --amend --author "Firstname Lastname <youremail@example.com>"
git rebase --continue
```
