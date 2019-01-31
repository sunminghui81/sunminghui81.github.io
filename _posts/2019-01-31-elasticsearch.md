---
layout: post
keywords: Elasticsearch, Kibana
description:  Elasticsearch 和 Kibana 安装笔记
title: "在 macOS 上安装  Elasticsearch 和 Kibana"
categories: [data-mining]
tags: [elasticsearch, kibana]
group: archive
icon: rocket
---
{% include mathsyouth/setup %}


###  macOS 安装 Elasticsearch 和 Kibana

Kibana 是 Elasticsearch 的 GUI 工具，安装命令为：
```
brew update
brew install elasticsearch
brew install kibana
```

查看安装信息（比如：安装路径和配置文件路径）：
```
brew info elasticsearch
brew info kibana
```

### 启动 Elasticsearch 和 Kibana

首先，启动 Elasticsearch:

1. 启动服务端：`brew services start elasticsearch`，关闭服务端：`brew services stop elasticsearch`
1. 直接启动服务端：`elasticsearch`
2. 客户端：`curl localhost:9200` 或者本地浏览器访问: `http://localhost:9200`

然后，启动 Kibana:

1. 启动服务端：`brew services start kibana`，关闭服务端：`brew services stop kibana`
1. 直接启动服务端：`kibana`
2. 客户端：`curl localhost:5601` 或者本地浏览器访问: `http://localhost:5601`

### 参考资料

1. [【Elasticsearch系列】1. Elasticsearch是什么？](https://www.jianshu.com/p/00035e5c624b)
1. [ES系列-3. ElasticSearch的Mapping](https://www.jianshu.com/p/c13e9c75ac02)
1. [Elasticsearch学习总结--原理篇](http://www.shaheng.me/blog/2015/06/elasticsearch--.html)
1. [Mongodb与Elasticsearch实时同步](http://blog.csdn.net/ACxlm/article/details/76473537#3elasticsearch-%E4%B8%8Emongodb%E5%BC%80%E5%90%AF%E5%90%8C%E6%AD%A5)
1. [搭建ElasticSearch+MongoDB检索系统](https://www.cnblogs.com/jamespei/p/5694495.html)
1. [mongo-connector](https://github.com/mongodb-labs/mongo-connector)
1. [python实现MongoDB数据同步到Elasticsearch](http://blog.csdn.net/zwq912318834/article/details/78647580?locationNum=2&fps=1)