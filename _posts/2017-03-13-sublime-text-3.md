---
layout: post
keywords: Sublime Text 3
description: Sublime Text 3 配置使用
title: "Sublime Text 3 配置使用"
categories: [tool]
tags: [Sublime Text 3]
group: archive
icon: key
---
{% include mathsyouth/setup %}


### Ubuntu 14.04 下 Sublime Text 3 的配置使用

#### 安装 Sublime Text 3

到 [Sublime Text 3](https://www.sublimetext.com/3) 官网上下载 Ubuntu 下对应的版本 [Ubuntu 64 bit](https://download.sublimetext.com/sublime-text_build-3126_amd64.deb)，然后执行命令：

```
sudo dpkg --install sublime-text_build-3126_amd64.deb
```

#### Sublime Text 3 [快捷键](https://segmentfault.com/a/1190000002570753)

`Ctrl` + `P`：搜索项目中的文件
`Ctrl` + `G`：跳转到第几行
`Ctrl` + `W`：关闭当前打开文件
`Ctrl` + `Shift` + `W`：关闭所有打开文件
`Ctrl` + `L`：选择行，重复可依次增加选择下一行
`Ctrl` +` Shift` + `L`：选择多行
`Ctrl` + `Shift` + `Enter`：在当前行前插入新行
`Ctrl` + `X`：删除当前行
`Ctrl` + `M`：跳转到对应括号
`Ctrl` + `R`：前往 method
`Ctrl` + `N`：新建窗口
`Ctrl` + `/`：注释当前行
`Ctrl` + `Shift`+ `/`：当前位置插入注释
`F11`：开关全屏
`Shift` + `F11`：开关全屏免打扰模式，只编辑当前文件
`Alt` + 数字：切换打开第N个文件
`Ctrl` + `Shift`+ 上下键：替换行

#### 解决无法输入中文的问题

##### fcitx 键盘输入法系统

解决方法请参考 [sublime-text-imfix](https://github.com/lyfeyaj/sublime-text-imfix)。

搜狗拼音采用的是 fcitx，所以如果安装了搜狗可以采用该方法。但是搜狗很耗 CPU，详细信息请参考[链接](https://github.com/fcitx/fcitx/issues/183https://github.com/fcitx/fcitx/issues/183vv)。

##### ibus 键盘输入法系统

点击[链接](https://github.com/xgenvn/InputHelper)安装 [InputHelper](https://github.com/xgenvn/InputHelper) 插件，关于如何安装 Package Control 可参考后文。

如果发现在 Sublime Text 3 中无法使用 `Ctrl` + `Shift`+ `Z` 调出输入框，请检查是否安装 `pygtk`:

```
sudo apt-get install python-gtk2-dev
```

#### 安装 Package Control

以后用到的插件都要借助 `Package Control` 来安装，打开已经安装好的 Sublime Text 3，然后点击顶部菜单的 View > Show Console（或者使用快捷键 `Ctrl` + `` ` ``）。在弹出的输入框里复制粘贴下面的代码：

```python
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```

重启 Sublime Text 3。如果看到 Preferences > Package Control，则安装成功。

安装插件的步骤：

1. 按下 `Ctrl` + `Shift` + `P`　调出命令面板；
1. 输入 `install package` 并回车，会列出可安装的插件；
1. 输入要安装的插件名称，比如 `MarkdownEditing`，并回车，插件会被安装；
1. 重启 Sublime Text 3 以完成安装。

卸载插件的步骤：

1. 按下 `Ctrl` + `Shift` + `P`　调出命令面板；
1. 输入 `remove package` 并回车，会列出已安装的插件；
1. 输入要删除的插件名称，比如 `MarkdownEditing`，并回车，插件会被删除；
1. 重启 Sublime Text 3 以完成卸载。

升级插件的步骤：

1. 按下 `Ctrl` + `Shift` + `P`　调出命令面板；
1. 输入 `upgrade package` 并回车，如果有列表出来则有插件需要更新；否则，会弹出提示框提醒没有任何插件需要更新；
1. 重启 Sublime Text 3 以完成升级。

#### Sublime Text 3 插件

##### [MarkDown Editing](https://github.com/SublimeText-Markdown/MarkdownEditing)

这个插件通过适当的颜色高亮和其它功能来更好地编辑 Markdown 文件，详细使用情况请点击 [MarkDown Editing](https://github.com/SublimeText-Markdown/MarkdownEditing)。

##### [Markdown Preview](https://github.com/revolunet/sublimetext-markdown-preview)

Markdown 预览工具，可以在网页上直接显示。

##### [SideBarEnhancements](https://packagecontrol.io/packages/SideBarEnhancements)

SideBarEnhancements 扩展了侧边栏中菜单选项的数量，提供诸如”New file”， “Duplicate” 和　“Delete” 等功能。"Delete" 功能将删除文件放入回收站，当你没有使用这样的功能而彻底删除了一个文件的时候，除非你用了版本管理软件，否则将很难恢复这个文件。

##### [Git](https://github.com/kemayo/sublime-text-git)

版本库是软件开发中不可缺少的工具，该插件把 Git 常用命令加入了 Command Palette，让开发人员进行代码管理方便不少。

##### [Git​Gutter](https://packagecontrol.io/packages/GitGutter)

这个小插件是在修改后的文件行号前增加一些标识图片，方便与版本库对比，修改内容一目了然。

##### [ConvertToUTF8](https://github.com/seanliang/ConvertToUTF8/blob/master/README.zh_CN.md)

通过插件 ConvertToUTF8，可以编辑并保存目前编码不被 Sublime Text 3 支持的文件，特别是中日韩用户使用的 GB2312，GBK，BIG5，EUC-KR，EUC-JP 等。

##### [BracketHighlighter](https://github.com/facelessuser/BracketHighlighter)

括号和标签高亮显示的插件

##### [spacegray](https://github.com/kkga/spacegray/)

A Hyperminimal UI Theme for Sublime Text. Activate the UI theme and color scheme by modifying your user preferences file, which you can find using the menu item Preferences -> Settings - User:

```
{   
    // Settings for Spacegray
    "color_scheme": "Packages/User/SublimeLinter/base16-ocean.dark (SL).tmTheme",
    // Hide navigation icons in Sublime Text 3
    "enable_tab_scrolling": false,
    "ignored_packages":
    [
        "Vintage"
    ],
    // Enable sidebar fileicons (only works in Sublime Text 3)
    "spacegray_fileicons": true,
    // Sidebar labels font size
    "spacegray_sidebar_font_large": true,
    // Sidebar tree rows height
    "spacegray_sidebar_tree_normal": true,
    // Tabs width
    "spacegray_tabs_auto_width": true,
    // Tab labels font size
    "spacegray_tabs_font_large": true,
    // Tabs height
    "spacegray_tabs_large": true,
    // Settings for Spacegray
    "theme": "Spacegray.sublime-theme",
    // 侧边栏文件夹显示加粗，区别于文件
    "bold_folder_labels": true,
    // 使用 unix 风格的换行符
    "default_line_ending": "unix",
    // 覆盖操作系统设置的DPI，否则标签上的中文显示为方块
    "dpi_scale": 1.0,
    // 保证在文件保存时，在结尾插入一个换行符。
    // 这样 git 提交时不会生产额外的 diff
    "ensure_newline_at_eof_on_save": true,
    // 右侧代码预览时给所在区域加上边框
    "draw_minimap_border": true,
    // 设置字体　"font_face": "Consolas",　导致换行失效。
    "font_size": 13,
    // 当前行高亮
    "highlight_line": true,
    // 高亮未保存文件
    "highlight_modified_tabs": true,
    // 自动移除行尾多余空格
    "trim_trailing_white_space_on_save": true,
    "trim_automatic_white_space": true,
    // Tab转空格
    "translate_tabs_to_spaces": true,
    // 是否显示行号
    "line_numbers": true,
    // 是否显示行号边栏
    "gutter": true,
    "word_wrap": true,
    "wrap_width": 80
}
```

##### [Emmet](https://github.com/sergeche/emmet-sublime)

前端必备神器，提高 HTML 和 CSS3 编写速度。

##### [Anaconda](http://damnwidget.github.io/anaconda/)

Anaconda 把 Pyflakes, pep8 和 McCabe 以插件的方式集成起来。安装 Anaconda 后，通过配置即可完成一个良好的 Python 开发环境。

##### [SublimeCodeIntel](https://github.com/SublimeCodeIntel/SublimeCodeIntel)

SublimeCodeIntel 为部分语言增强自动完成+成员/方法提示功能，包括了 Python。Additional settings can be configured in the User File Settings:

Do NOT edit the default SublimeCodeIntel settings. Your changes will be lost when SublimeCodeIntel is updated. ALWAYS edit the user SublimeCodeIntel settings by selecting "Preferences -> Package Settings -> SublimeCodeIntel ->Settings - User". Note that individual settings you include in your user settings will completely replace the corresponding default setting, so you must provide that setting in its entirety.

```
{
    "Python": {
        "python": "/usr/bin/python",
        "pythonExtraPaths": []
    },
    "Python3": {
        "python": "/usr/bin/python3",
        "pythonExtraPaths": []
    }
}
```

##### [sublime-rst-completion](https://github.com/mgaitan/sublime-rst-completion)

编写 RST 文档的利器。
