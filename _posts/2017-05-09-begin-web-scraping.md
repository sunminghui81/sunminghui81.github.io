---
layout: post
keywords: web scraping, web crawling
description: 网页爬虫学习总结
title: 网页爬虫零起步
categories: [web scraping]
tags: [web scraping, web crawling]
group: archive
icon: file
---
{% include mathsyouth/setup %}


### 了解网站 `robots.txt` 和 `Sitemap`

在爬取一个网站的内容之前，我们应该首先对该网站的规模和结构有一定的了解，而网站的 `robots.txt` 和 `Sitemap` 文件可以给我们提供一些知道。其中， `robots.txt` 用来声明该网站中不想被搜索引擎收录的部分；`Sitemap` 可以方便搜索引擎蜘蛛抓取网站页面，通过抓取网站页面，清晰了解网站的架构，一般存放在根目录下，为搜索引擎蜘蛛指路，增加网站重要内容页面的收录。

### 参考文献

1. [百度首页 -- 禁止搜索引擎收录的方法](http://bar.baidu.com/robots/)
1. [如何使用robots.txt](https://china.googleblog.com/2008/04/robotstxt_3856.html)
1. [了解站点地图](https://support.google.com/webmasters/answer/156184?hl=zh-Hans)