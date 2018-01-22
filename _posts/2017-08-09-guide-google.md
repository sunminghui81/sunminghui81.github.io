---
layout: post
keywords: Google
description: 谷歌搜索引擎使用指南
title: "高效使用谷歌搜索引擎"
categories: [tool]
tags: [Google]
group: archive
icon: key
---
{% include mathsyouth/setup %}


### 常用技巧

#### 使用 `""` 进行完全匹配

使用方法："关键字"，通过给关键字加双引号的方法，得到的搜索结果就是完全按照关键字的顺序来搜。
例如：mac "microsoft office"，谷歌会返回包括词组 microsoft office 和 词 mac 的链接

#### 使用 `-` 排除关键字

使用方法：关键字 -排除关键字 ， Tip：`-` 前面有空格，但后面没有空格。

#### 使用  `*`  进行模糊匹配

使用方法："关键字 * 关键字"。比如："我还 * 不能够"，`*`表示任何文字。

#### 使用 `site` 指定网站

使用方法：`site:web_address search_term`。比如：site:douban.com 现代操作系统

#### 使用 `related` 返回与某个关键词或者网站有关联的链接

比如：`related: k8s`，返回和 `k8s` 相关的技术；`related: baidu.com` 返回和百度相关的信息。

#### 使用 `filetype` 指定文件类型

使用方法：`filetype:file_type search_term`。比如：filetype:pdf 现代操作系统

#### 使用 `intitle` 和 `allintitle` 指定标题

1. `intitle` 指令返回的是页面title 中包含关键词的页面，使用方法：`intitle:search_term`。比如：intitle:现代操作系统
2. `allintitle` 搜索返回的是页面标题中包含多组关键词的文件。例如 ：`allintitle:SEO 搜索引擎优化`，就相当于：`intitle:SEO intitle:搜索引擎优化`，返回的是标题中中既包含“SEO”，也包含“搜索引擎优化”的页面。

#### 使用 `inurl` 和 `allinurl` 指定链接

1. `inurl` 指令用于搜索查询词出现在url 中的页面，使用方法：`inurl:search_term`。比如：inurl:modern operating systems；还如：`inurl:gov 减肥`，返回的就是url 中包含gov，页面中有“减肥”这个词的页面。`inurl:.中国教育和科研计算机网CERNET intitle:交换链接` 返回的则是来自中国教育和科研计算机网CERNET 域名，标题中包含“交换链接”这四个字的页面。
2. `allinurl` 与 `allintitle`  类似。例如：`allinurl:SEO 搜索引擎优化`，就相当于 ：`inurl:SEO inurl:搜索引擎优化`。

#### 使用 `inanchor` 

`inanchor`: 指令返回的结果是导入链接锚文字中包含搜索词的页面，比如在Google 搜索 ：`inanchor:点击这里`，返回的结果页面本身并不一定包含“点击这里”这四个字，而是指向这些页面的链接锚文字中出现了“点击这里”这四个字。

#### 使用 `link` 搜索出链接到该主页的网页

比如：`link:www.sogou.com`

#### 使用 `@` 搜索社交媒体

将 `@` 放在社交媒体词的前面，比如：`@weibo` 或者 `@twitter`

#### 使用 `OR` 进行组合搜索

将 `OR` 放在搜索词之间，比如：`marathon OR race`

### 参考资料

1. [Refine web searches](https://support.google.com/websearch/answer/2466433)
2. [Google搜索技巧](http://www.jianshu.com/p/badee8f043e5)
3. [如何用好谷歌等搜索引擎](https://www.zhihu.com/question/20161362)