---
layout: post
keywords: macOS, matplotlib
description: 解决 macOS 系统上 matplotlib 中文显示乱码问题
title: "解决 macOS 系统上 matplotlib 中文显示乱码问题"
categories: [python]
tags: [macos, matplotlib]
group: archive
icon: compass
---
{% include mathsyouth/setup %}

### matplotlib 中文显示乱码问题

`matplotlib` 中文显示乱码，并提示警告：
```
/Users/linghui/.local/share/virtualenvs/projects-R8MYRK6K/lib/python3.6/site-packages/matplotlib/backends/backend_agg.py:180: RuntimeWarning: Glyph 29289 missing from current font.
  font.set_text(s, 0, flags=flags)
```

### 方法一：下载新的字体（相对方法二操作复杂很多）

1. 下载中文字体，比如：黑体（注意系统版本），[链接](https://www.fontpalace.com/font-details/SimHei/)，解压之后在系统当中安装好，打开字体册就可以安装；

2. 运行命令
```
import matplotlib
matplotlib.matplotlib_fname()
```
得到 matplotlibrc 配置文件路径，比如：
```
/Users/linghui/.local/share/virtualenvs/projects-R8MYRK6K/lib/python3.6/site-packages/matplotlib/mpl-data/matplotlibrc
```
字体目录就在与 matplotlibrc 文件同级的 font/ttf 目录中，即字体存放目录的完整路径如下
```
/Users/linghui/.local/share/virtualenvs/projects-R8MYRK6K/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/
```

3. 将下载好的字体拷贝到字体存放目录中
```
cp ~/Downloads/SimHei.ttf /Users/linghui/.local/share/virtualenvs/projects-R8MYRK6K/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/
```

4. 全局设置参数 -- 修改 matplotlibrc 配置文件（或者第5步动态设置参数，二者选一即可）
```
vim /Users/linghui/.local/share/virtualenvs/projects-R8MYRK6K/lib/python3.6/site-packages/matplotlib/mpl-data/matplotlibrc
```
首先，将
```
#font.family         : sans-serif
```
前面的`#`注释号去掉；其次，将
```
#font.sans-serif     : DejaVu Sans, Bitstream Vera Sans, Computer Modern Sans Serif, Lucida Grande, Verdana, Geneva, Lucid, Arial, Helvetica, Avant Garde, sans-serif
```
`SimHei` 添加在最前面，其中`SimHei` 代表黑体，并将前面的`#`注释号去掉；最后，将
```
#axes.unicode_minus :True
```
前面的`#`注释号去掉，并将 `True` 改为 `False`

5. 静态设置参数 -- 添加如下代码（或者第4步动全局设置参数，二者选一即可）
```python
import matplotlib
# 指定默认字体
matplotlib.rcParams['font.sans-serif'] = ['SimHei'] 
matplotlib.rcParams['font.family'] ='sans-serif'
# 解决负号'-'显示为方块的问题
matplotlib.rcParams['axes.unicode_minus'] = False 
```

6. 清空缓存
```shell
rm -rf ~/.matplotlib/*.cache 
```

7. 改了配置之后并不会生效，需要重新加载字体，在 Python 中运行如下代码
```python
from matplotlib.font_manager import _rebuild
_rebuild()
```

### 方法二：利用系统已用字体（相对方法一操作简单很多）

1. 利用 Unix-like 命令查找系统已经存在的字体
```shell
fc-list :lang=zh
```

2. 添加如下代码
```python
from matplotlib import rcParams
from matplotlib.font_manager import FontProperties
import matplotlib.pyplot as plt
# 解决负号'-'显示为方块的问题
rcParams['axes.unicode_minus']=False
myfont = FontProperties(fname='/Library/Fonts/Songti.ttc',
    size=15)
plt.title('乘客等级分布', fontproperties=myfont)
plt.ylabel('人数', fontproperties=myfont)
plt.legend(('头等舱', '二等舱', '三等舱'), loc='best', prop=myfont)
```

### 参考文献
1. [解决macOS下matplotlib无法显示中文的问题](https://www.jianshu.com/p/8ed59ac76c06)
2. [彻底解决matplotlib中文乱码问题](https://blog.csdn.net/dgatiger/article/details/50414549)
