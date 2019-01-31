---
layout: post
keywords: macOS, install, update, uninstall
description: macOS High Sierra 安装、更新和卸载应用
title: macOS High Sierra 安装、更新和卸载应用
categories: [unix-based]
tags: [macOS, install, update, uninstall]
group: archive
icon: linux
---
{% include mathsyouth/setup %}


### 官方指导文档

[macOS High Sierra 安装、更新和卸载应用](https://support.apple.com/zh-cn/guide/mac-help/mh35835)

### 卸载不在 App Store 上下载的软件

1. 进入到 Finder -> 应用程序 -> 删除应用程序
2. `cd /Users/admin/Library/Preferences` 删除偏好设置相关的 plist
3. `cd /Users/admin/Library/Application Support` 删除相关的支持文件
4. `cd /Users/admin/Library/Caches/Metadata` 删除相关的支持文件
5. `cd /Users/admin/Library/Saved Application State` 删除相关的支持文件

### 第三方软件管理工具

利用 `homebrew` 软件管理应用

### 第三方卸载软件

Dr. Cleaner, CleanMyMac

### 参考文献

1. [如何在Mac上卸载程序](https://zh.wikihow.com/%E5%9C%A8Mac%E4%B8%8A%E5%8D%B8%E8%BD%BD%E7%A8%8B%E5%BA%8F)