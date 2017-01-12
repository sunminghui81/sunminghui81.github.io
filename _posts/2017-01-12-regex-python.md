---
layout: post
keywords: re, regex
description: python 正则化表达式的学习总结
title: "总结 python 正则化表达式常见用法"
categories: [python]
tags: [re, regex]
group: archive
icon: compass
---
{% include mathsyouth/setup %}

### 通过练习学习正则表达式

1. `\[[^]]+\]`: `\[`匹配开方括号，然后用一个排除型字符组`^`匹配除闭方括号 ] 之外的任意字
符（注意，在字符组内部，闭方括号 `]` 一定需要转义），用`+`表示它至少要出现一次以上，最后用一个`\]`匹配闭方括号。
1. `(?<=\[)[^]]+(?=\])`： `(?<=\[)` 匹配`\[`之后, `(?=\])`匹配`\]`之前。 <br>

   ```python
   import re
   # 进行数据验证时，在表达式首尾加上^和$是好习惯
   sample = ("this is a [sample] string with [some] "
             "special words. [another one]")
   re.search(r"\[[^]]+\]", sample).group() # '[sample]'
   re.search(r"(?<=\[)[^]]+(?=\])", sample).group() # 'sample'
   re.search(r"\\section", "\section").group() # "\\section"
   re.search("\\\\section", "\section").group() # "\\section"
   ```

1. "/foo/bar_tmp.php" 中 foo 是模块名，bar 是控制器名，tmp 是方法名。要求只出现控制器名
（/foo），也可以只出现控制器名和模块名（/foo/bar.php），也可以3者都出现
（/foo/bar_tmp.php）。表达式为 `/foo(?:/bar(?:_tmp){0,1}.php){0,1}?`，`(?:...)`
表示匹配 () 内的字符串，但是这些字符串匹配完后无法被提取； `{0,1}`表示重复0或１次，如果用`?`
替换，结果完全一样。<br>

   ```python
   import re
   sample = "/foo, /foo/bar.php, /foo/bar_tmp.php"
   re.findall("/foo(?:/bar(?:_tmp){0,1}.php){0,1}", sample)
   re.findall("/foo(?:/bar(?:_tmp)?.php)?", sample)
   ```

1. `(?!c[ua]t)c[a-z]+t` 匹配 c 开头、t 结尾的单词，但不希望匹配 cut 和 cat。<br>

   ```python
   import　re
   sample = "chart, conduct, court, cat, cut"
   re.findall(r'(?!c[ua]t)c[a-z]+t', sample)
   ```

### [编码基础知识][Unicode]

通常来说，英文编码较为统一，往往采用 ascii 编码或兼容 ascii 的编码（即编码表的前127位与
ascii 编码一致，常用的各种编码，包括 Unicode 编码都是如此）。也就是说，英文字母、阿拉伯数
字和英文的各种符号，在不同编码下的表示是一样的，比如字母 A，其编码总是41，常见的编码中，英文
字符和半角标点符号的编码都等于 ascii 编码，通常只用一个字节表示。

但是中文的情况则不同，常见的中文编码有 GBK（CP936） 和 Unicode 两种，同一个中文字符在不同
编码下的值并不相同，比如“发”字，GBK 编码的值为 b7 a2，用两个字节表示；而 Unicode 编码的值
（也就是代码点，Code Point）为53 d1。如果用 UTF-8 编码保存，需要3个字节（e5 8f 91）；用 UTF-16 编码保存，需要4个字节（53 d1）。

虽然常见的中文字符编码有 GBK 和 Unicode 两种，但如果需要使用正则表达式处理中文，强烈推荐使
用 Unicode 字符，不仅是因为正则表达式提供了对 Unicode 的现成支持，而且因为 GBK 编码可能会
有其它问题。


### Reference

1. [Regular Expression HOWTO](https://docs.python.org/2/howto/regex.html)
1. [Python Regular Expressions](https://developers.google.com/edu/python/regular-expressions)
1. [正则表达式（一）：纠结的转义](http://www.infoq.com/cn/news/2011/01/regular-expressions-1)
1. [Unicode 诸问题（上）][Unicode]
1. [正则表达式（四）：正则表达式的与或非](http://www.infoq.com/cn/news/2011/04/regular-expressions-4)
1. [使用 Python 模块 re 实现解析小工具](https://www.ibm.com/developerworks/cn/opensource/os-cn-pythonre/)

[Unicode]: http://www.infoq.com/cn/news/2011/02/regular-expressions-unicode