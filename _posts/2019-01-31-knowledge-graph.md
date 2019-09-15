---
layout: post
keywords: knowledge graph
description: 收集有关知识图谱的经典入门资料，以供参考
title: "知识图谱入门资料"
categories: [NLP]
tags: [knowledge-graph, kg]
group: archive
icon: comment
---
{% include mathsyouth/setup %}


### 图数据库的选型

1. https://www.g2crowd.com/categories/graph-databases
2. [ArangoDB Reviews](https://www.g2crowd.com/products/arangodb/reviews)
3. https://www.arangodb.com/why-arangodb/arangodb-vs-neo4j/
4. [NoSQL Performance Benchmark 2018 – MongoDB, PostgreSQL, OrientDB, Neo4j and ArangoDB](https://www.arangodb.com/2018/02/nosql-performance-benchmark-2018-mongodb-postgresql-orientdb-neo4j-arangodb/)
5. [CG Pipeline: The Best Graph Database for Your CG Production Data](https://medium.com/cgwire/cg-pipeline-the-best-graph-database-for-your-cg-production-data-4cedc9e49065)
6. [DB-Engines Ranking](https://db-engines.com/en/ranking) 数据库排名网站


### ArangoDB

#### Mac 上安装 ArangoDB 和登陆

1. Homebrew 安装 ArangoDB: `brew install arangodb`
2. 修改密码: `/usr/local/opt/arangodb/sbin/arango-secure-installation` 为 `root`
3. To have launchd start arangodb now and restart at login:
```shell
brew services start arangodb 
```
Or, if you don't want/need a background service you can just run:
```shell
/usr/local/opt/arangodb/sbin/arangod
```
4. Web 界面登陆 `http://localhost:8529`
5. ArangoDB Shell 客户端登陆：`arangosh`

#### Install and Configure ArangoDB on Ubuntu 16.04

Before starting, it is recommended to update your system to the latest stable version with the following commands:
```
sudo apt-get update -y
sudo apt-get upgrade -y
```

Then you will need to download the public key from the ArangoDB site to set up the ArangoDB repository. Run the following command to download the public key:
```
curl -OL https://download.arangodb.com/arangodb33/xUbuntu_16.04/Release.key
sudo apt-key add - < Release.key
```

Use apt-get to install arangodb:
```
echo 'deb https://download.arangodb.com/arangodb33/xUbuntu_16.04/ /' | sudo tee /etc/apt/sources.list.d/arangodb.list
sudo apt-get install apt-transport-https
sudo apt-get update -y
sudo apt-get install arangodb3=3.3.16
```

To install the debug symbols package (not required by default)
```
sudo apt-get install arangodb3-dbg=3.3.16
```

Once the installation is complete, start the arangodb3 service with the following command:
```
sudo systemctl start arangodb3
```
You can also check the status of ArangoDB with the following command:
```
sudo systemctl status arangodb3
```

You will see output similar to the following.
```
arangodb3.service - LSB: arangodb
   Loaded: loaded (/etc/init.d/arangodb3; bad; vendor preset: enabled)
   Active: active (running) since Thu 2018-09-27 15:20:56 CST; 3min 53s ago
     Docs: man:systemd-sysv-generator(8)
  Process: 28548 ExecStart=/etc/init.d/arangodb3 start (code=exited, status=0/SUCCESS)
    Tasks: 24
   Memory: 229.0M
      CPU: 1.381s
   CGroup: /system.slice/arangodb3.service
           ├─28603 /usr/sbin/arangod --uid arangodb --gid arangodb --pid-file /var/run/arangodb/arangod.pid --temp.path /var/tmp/arangod --log.foreground-tty fa
           └─28604 /usr/sbin/arangod --uid arangodb --gid arangodb --pid-file /var/run/arangodb/arangod.pid --temp.path /var/tmp/arangod --log.foreground-tty fa

Sep 27 15:20:54 VM-16-13-ubuntu systemd[1]: Starting LSB: arangodb...
Sep 27 15:20:54 VM-16-13-ubuntu arangodb3[28548]:  * Starting arango database server arangod
Sep 27 15:20:56 VM-16-13-ubuntu arangodb3[28548]: {startup} starting up in daemon mode
Sep 27 15:20:56 VM-16-13-ubuntu arangodb3[28548]:    ...done.
Sep 27 15:20:56 VM-16-13-ubuntu systemd[1]: Started LSB: arangodb.
Sep 27 15:24:37 VM-16-13-ubuntu systemd[1]: Started LSB: arangodb.
```

#### Access ArangoDB CLI
ArangoDB comes with a built-in arangosh command line utility to access the database. Start `arangosh`

#### ArangoDB web interface

ArangoDB comes with a built-in web interface for performing various administrative tasks. Before starting, you will need to edit the ArangoDB configuration files `arangod.conf` and `arangosh.conf`:
```
sudo vim /etc/arangodb3/arangod.conf
```

运行 `ifconfig`，查看服务器的内网 IP (inet addr:172.17.16.13), 然后添加内网 IP:

```
endpoint = tcp://172.17.16.13:8529
```

Once you are finished, open the other configuration file:
```
sudo vim  /etc/arangodb3/arangosh.conf
```
Again, add your server's IP address.
```
endpoint = tcp://172.17.16.13:8529
```

Save the file and restart the ArangoDB service:
```
sudo systemctl restart arangodb3
```

##### Firewall update
By default, ArangoDB runs on port 8529, so you will need to allow this port through the firewall. You can do this by running the following command:
```
sudo ufw allow 8529/tcp
```
Once you are finished, it's time to access ArangoDB web interface.

打开浏览器，输入外网 IP http://192.168.0.227:8529.  This will open up the login screen for the `_system` db. After entering your login credentials, you will see the ArangoDB splash screen.


### 知识图谱入门介绍

#### 博客简介

1. [图形数据库Neo4J简介](https://www.cnblogs.com/loveis715/p/5277051.html)
2. [知识图谱基础（三）-schema的构建](https://www.jianshu.com/p/704e935c98a9)
3. [知识图谱基础之RDF，RDFS与OWL](https://zhuanlan.zhihu.com/p/32122644)
4. [图数据库查询语言Cypher、Gremlin和SPARQL](https://blog.csdn.net/wzwdcld/article/details/80783782)

#### 视频简介

1. [4-王昊奋-知识图谱关键技术及行业应用浅析](https://www.bilibili.com/video/av28256339/?spm_id_from=333.788.videocard.9)


### 知识图谱存储

#### RDF 存储

1. 数据库 [Apache Jena](https://jena.apache.org/)，使用该数据库的经典案例：
   * [Acemap Knowledge Graph](https://acemap.info/app/AceKG/) 重点参考如何构建知识图谱；
   * [Z_knowledge_graph](https://github.com/Pelhans/Z_knowledge_graph) 从零开始的知识图谱生活；
   * [KGQA-Based-On-medicine](https://github.com/YeYzheng/KGQA-Based-On-medicine) 基于医药知识图谱的智能问答系统
2. 数据库 [GraphEngine](https://github.com/Microsoft/GraphEngine)，前身为[Trinity](https://www.microsoft.com/en-us/research/project/trinity/?from=http%3A%2F%2Fresearch.microsoft.com%2Fen-us%2Fprojects%2Ftrinity%2F)，使用该数据库的经典案例
    * [ Trinity.RDF](http://www.vldb.org/pvldb/vol6/p265-zeng.pdf) A Distributed Graph Engine for Web Scale RDF Data
    * [Trinity Graph Engine and its Applications](https://www.graphengine.io/downloads/papers/TrinityAndApps.pdf)

#### 备选图数据存储

1. [janusgraph](https://github.com/JanusGraph/janusgraph) 前身为 [titan](https://github.com/thinkaurelius/titan)，使用案例
   * [LeapTalk Dr. Muthusrinivasan on Uber Knowledge Graph](https://www.youtube.com/watch?v=C01Gh0g01JE)
2. [Dgraph](https://dgraph.io/)

#### 图数据库对比

1. [Graph databases and their application to the Italian Business Register for efficient search of relationships among companies](http://tesi.cab.unipd.it/54610/1/luca_sinico_tesi.pdf)
2. [Neo4j vs Dgraph - The numbers speak for themselves](https://blog.dgraph.io/post/benchmark-neo4j/)


### 知识图谱工具

#### 关系抽取工具

1. [deepdive](https://github.com/HazyResearch/deepdive) 主要针对关系抽取，在指定的关系抽取中效果比较理想，在实体确定后可以很好地进行关系抽取；
2. [snorkel](https://github.com/HazyResearch/snorkel)
3. [fonduer](https://github.com/HazyResearch/fonduer)
4. [MIML-RE](https://nlp.stanford.edu/software/mimlre.shtml)
5. [MultiR](http://aiweb.cs.washington.edu/ai/raphaelh/mr/)
6. [NELL](http://rtw.ml.cmu.edu/rtw/)
7. [Dependency Kernel](https://www.cs.utexas.edu/~ml/papers/spk-emnlp-05.pdf)
8. [Open IE 5.0](https://github.com/dair-iitd/OpenIE-standalone)
9. [PROSPERA](https://www.mpi-inf.mpg.de/departments/databases-and-information-systems/research/yago-naga/prospera/)
10. [CoType](https://github.com/INK-USC/DS-RelationExtraction)
11. [ReQuest](https://github.com/INK-USC/ReQuest)
12. [GraphLSTM](http://hanover.azurewebsites.net/)


#### 命名实体识别工具

1. NLPIR、LTP、FudanNLP、[python-stanford-corenlp](https://github.com/stanfordnlp/python-stanford-corenlp)
2. [Stanford Named Entity Recognizer (NER)](https://nlp.stanford.edu/software/CRF-NER.shtml)
3. [Illinois Named Entity Tagger](https://cogcomp.org/page/software_view/NETagger)
2. [Fine-Grained Entity Recognizer](https://github.com/xiaoling/figer)
3. [MITIE: MIT Information Extraction](https://github.com/mit-nlp/MITIE)
4. [中文实体识别与关系提取](https://github.com/crownpku/Information-Extraction-Chinese)
5. [ClusType](https://github.com/INK-USC/ClusType)
6. [CoType](https://github.com/RaniemAR/CoType)
7. [LM-LSTM-CRF](https://github.com/yuzhimanhua/LM-LSTM-CRF)
8. [CrossType Name Tagger](https://github.com/yuzhimanhua/LM-LSTM-CRF)
9. [Multi-tasking LSTM-CRF](https://github.com/hltcoe/golden-horse/) Named Entity Recognition for Chinese social media (Weibo)
10. [Cross-type Biomedical Named Entity Recognition](https://github.com/yuzhimanhua/lm-lstm-crf)

#### KB reasoning

1. [DeepPath](https://github.com/xwhan/DeepPath)
2. [KBGAN](https://github.com/cai-lw/KBGAN)
3. [ProPPR](https://github.com/TeamCohen/ProPPR)

#### 可视化工具

1. [D3.js](https://github.com/d3/d3)
2. [incubator-echarts](https://github.com/apache/incubator-echarts)
3. [pyecharts](https://github.com/pyecharts/pyecharts)


### 知识图谱课程

1. [KnowledgeGraphCourse](https://github.com/npubird/KnowledgeGraphCourse) 东南大学《知识图谱》研究生课程。


### 开放的知识图谱数据集

1. [Agriculture_KnowledgeGraph](https://github.com/qq547276542/Agriculture_KnowledgeGraph) 农业知识图谱(AgriKG)：农业领域的信息检索，命名实体识别，关系抽取，智能问答，辅助决策
2. [Open Academic Graph](https://www.aminer.cn/open-academic-graph) is generated by linking two large academic graphs: Microsoft Academic Graph (MAG) and AMiner.
3. [Open Academic Graph 2019](https://www.aminer.cn/oag2019) is the second version of OAG which was generated in Jan 2019.
4. [stock-knowledge-graph](https://github.com/lemonhu/stock-knowledge-graph) 利用网络上公开的数据构建一个小型的证券知识图谱/知识库
5. [Diabetes-knowledge-graph](https://github.com/luluforever/Diabetes-knowledge-graph) 糖尿病知识图谱数据
6. [QASystemOnMedicalKG](https://github.com/liuhuanyong/QASystemOnMedicalKG) 知识图谱构建，自动问答，基于kg的自动问答。以疾病为中心的一定规模医药领域知识图谱，并以该知识图谱完成自动问答与分析服务。
7. [Acemap Knowledge Graph](https://www.acemap.info/app/AceKG/) 提供了近100G大小的数据集，为每个实体提供了丰富的属性信息，涵盖权威学术知识，旨在为众多学术大数据挖掘项目提供支持。
8. [cancer-KnowledgeGraph](https://github.com/ddddwy/cancer-KnowledgeGraph) a knowledge graph demo for cancer。


### 网络 debug

1. [ArangoDB Could not connect](https://stackoverflow.com/questions/32550229/arangodb-could-not-connect)

### 参考文献

1. [ArangoDB 入门指南](http://coyee.com/article/12446-arangodb-getting-started)