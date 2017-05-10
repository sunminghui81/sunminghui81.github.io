---
layout: post
keywords: Ubuntu, keyboard shortcuts
description: Ubuntu 14.04 常用键盘快捷键总结
title: Ubuntu 14.04 键盘快捷键
categories: [unix-based]
tags: [Ubuntu, keyboard shortcuts]
group: archive
icon: linux
---
{% include mathsyouth/setup %}


## 查找键盘快捷键

### 查找系统快捷键

点击右上角的主菜单，然后 System Settings > Keyboard > Shortcuts


### 查找应用程序特定的键盘快捷键

应用程序特定的键盘快捷键通常可以在屏幕顶部的应用程序的下拉菜单中找到。

注意: 下面的快捷键中， Super 键指 Win 键或苹果电脑的 Command 键。


## 桌面快捷键

* Super: 打开 Dash 面板，可搜索或浏览项目，默认有个搜索框，按“下”方向键进入浏览区域
* 在 Dash 面板中按 Ctrl + Tab: 切换到下一个子面板（可搜索不同类型项目，如程序、文件、音乐）
* Super + A: 搜索或浏览程序（Application）
* Super + F: 搜索或浏览文件（File）
* Super + M: 搜索或浏览音乐文件（Music）
* Alt + F1: 聚焦到桌面左侧任务导航栏，可按上下键导航。
* Alt + F2: 运行命令
* Alt + F4: 关闭窗口
* Alt + 空格: 打开窗口菜单
* PrtSc:  对屏幕进行截图
* Alt + PrtSc: 对当前窗口进行截图
* Shift + PrtSc: 对指定区域进行截图
* Super + W: 显示当前工作区中的所有窗口
* Alt + 鼠标左键: 移动焦点窗口
* Alt + 鼠标右键: 显示窗口菜单
* Super + S: 缩小，显示工作区切换器
* Alt + Tab: 切换程序窗口
* Ctrl + Alt + Tab: 在所有工作区当前打开的窗口之间切换, 松开 Ctrl 和 Alt 键切换到选定的窗口
* Ctrl + Alt + 上/下/左/右键:　工作区切换
* Ctrl + Super + 上: 最大化窗口
* Ctrl + Super + 下: 最小化窗口或使窗口从最大化恢复到原来的尺寸
* Ctrl + Super + Left: 在屏幕左半边纵向最大化窗口
* Ctrl + Super + Right: 在屏幕右半边纵向最大化窗口
* Ctrl + Alt + Shift + Left/Right/Up/Down: 将当前窗口移动到其他的工作区
* Ctrl + PgUp/PgDn: 在终端窗口的多个标签页(tab)之间切换
* Ctrl + Shift + PgUp/PgDn: 当终端窗口有多个标签页(tab)时，移动某个标签页的位置
* Alt + n: 选定终端窗口中的第 n 个标签页(n 是一个正整数)
* Ctrl + +/- 增大/缩小终端字体的大小


## 常见的应用程序快捷键

* Ctrl + C: 复制
* Ctrl + X: 剪切
* Ctrl + V: 粘贴
* Ctrl + A: 全选
* Ctrl + N: 打开一个新的文档或窗口
* Ctrl + S: 保存当前文档
* Ctrl + O: 打开另外一个文档
* Ctrl + Z: 撤销最近的更改
* Ctrl + Shift + Z: 重做刚刚撤销的动作


## 系统快捷键

* Ctrl + Alt + Delete: 锁屏或登出
* Ctrl + Alt + L: 锁屏
* `Ctrl` + `Alt` + `F1`: 进入 tty1，输入用户名和密码登录
* `Ctrl` + `Alt` + `F7`: 退出 tty1，返回桌面
* `pkill Xorg` 或 `restart lightdm`：注销桌面重新登录


## Terminal 终端

* Ctrl + Alt + T: 打开终端
* Ctrl + Shift + T: 新建标签页
* Tab: 自动补全命令或文件名
* Ctrl + Shift + V: 粘贴 （Linux 中不需要复制的动作，文本被选择就自动被复制）
* Ctrl + D: 关闭标签页
* Ctrl + L: 清屏幕
* Ctrl + R + 文本: 在输入历史中搜索
* Ctrl + C: 终止当前任务


## 参考

1. [KeyboardShortcuts](https://help.ubuntu.com/community/KeyboardShortcuts)