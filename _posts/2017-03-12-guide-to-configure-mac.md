---
layout: post
keywords: macOS Sierra
description: 配置一个高效的 macOS Sierra 工作环境
title: "配置 macOS Sierra 工作环境"
categories: [tool]
tags: [macOS Sierra]
group: archive
icon: key
---
{% include mathsyouth/setup %}


## 常用工具

### [Homebrew](http://brew.sh)

包管理工具，官方称之为 "The missing package manager for OS X"。安装步骤见官网。

有了 brew 以后，要下载工具，比如 MySQL、Gradle、Maven、Node.js 等工具，就不需要去网上下载了，只要一行命令就能搞定：

```sh
brew install mysql gradle maven node
```

PS：安装 brew 的时候会自动下载和安装 Apple 的 Command Line Tools。

### [Homebrew Cask](http://caskroom.io)

`brew-cask`允许你使用命令行安装 OS X 应用，并从应用的官网上下载。比如可以这样安装 Chrome：`brew cask install google-chrome`，还有 Evernote、Sublime Text、VirtualBox 等都可以用 `brew-cask` 安装。

`brew-case` 安装步骤：

```
brew tap caskroom/cask
brew install brew-cask
```

`brew-case` 的使用可参考 [Homebrew Cask Homepage](http://gillesfabio.github.io/homebrew-cask-homepage/)。

应用也可以通过 App Store 安装，而且有些应用只能通过 App Store 安装，比如 Xcode 等一些 Apple 的应用。

### Sublime Text 3

```
brew cask install sublime-text
```

默认安装 Sublime Text version 3。

### MacDown

```
brew cask install macdown
```

MacDown 是 Markdown 编辑器。

### CheatSheet

```
brew cask install cheatsheet
```

CheatSheet 能够显示当前程序的快捷键列表，默认的快捷键是长按 `⌘`。

### Alfred

```
brew cask install alfred
```

Mac 用户不用鼠标键盘的必备神器，配合大量 Workflows，习惯之后可以大大减少操作时间。

上手简单，调教成本在后期自定义 Workflows，不过有大量雷锋使用者提供的现成扩展，访问[这里](http://www.alfredworkflow.com/)挑选喜欢的，并可以极其简单地根据自己的需要修改。

## 参考资料

1. [强迫症的 Mac 设置指南](https://github.com/macdao/ocds-guide-to-setting-up-mac/blob/master/README.md#ruby-%E5%B8%B8%E7%94%A8%E5%88%AB%E5%90%8D)
