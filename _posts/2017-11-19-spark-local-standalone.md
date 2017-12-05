---
layout: post
keywords: Spark, Standalone, local
description: Spark 安装笔记
title: "在 Ubuntu 16.04 服务器上安装 Spark Standalone（伪分布式）"
categories: [data-mining]
tags: [spark, standalone, local]
group: archive
icon: rocket
---
{% include mathsyouth/setup %}


### 配置工作

#### 生成配置文件

首先，利用 `conf/spark-env.sh.template` 文件来创建文件 `conf/spark-env.sh`：

```
cp conf/spark-env.sh.template conf/spark-env.sh
```

并通过该创建的文件来设置环境变量来配置集群：

```
# 创建6个 Worker
export SPARK_WORKER_INSTANCES=6
```

其次，创建 `conf/slaves.template` 文件：

```
cp conf/slaves.template conf/slaves
```

### 启动 Master 和 Worker

#### 启动 Master

通过执行下面的代码来启动一个 standalone master server：

```
./sbin/start-master.sh
```

运行命令 `ps -aux | grep spark` 查看 Master 是否启动成功，比如我的结果如下：

```
ai       32509  0.6  0.4 7667004 574832 pts/4  Sl   20:19   0:12 /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java -cp /usr/local/spark/conf/:/usr/local/spark/jars/* -Xmx1g org.apache.spark.deploy.master.Master --host dell-Precision-Tower-7910 --port 7077 --webui-port 8080
```

其中，`--host dell-Precision-Tower-7910` 表示监听的 Hostname 为 `dell-Precision-Tower-7910`，`--port 7077` 表示监听的服务端口为 `7077`（默认值），`--webui-port 8080` 表示 web UI 的端口为 `8080`（默认值）。

#### 启动 Worker

启动 workers 并且通过命令 `./sbin/start-slave.sh <master-spark-URL>` 连接到 master，根据前面的 `--host` 和 `--port` 的值，说明 `<master-spark-URL` 为 `spark://dell-Precision-Tower-7910:7077`，所以启动 workers 的命令为：
```
./sbin/start-slave.sh spark://dell-Precision-Tower-7910:7077 -c 2 -m 10g
```

其中，`-c 2` 表示 Spark 应用程序在机器上可以使用的全部 CPU 核数，`-m 10g` 表示 Spark 应用程序可以使用的内存数量为 `10G`，`-m MEM` 中 `MEM`的格式像 `10240M` 或者 `10G`。

### 提交应用程序到集群中

最后，我们可以提交应用程序了：

```
spark-submit --master spark://dell-Precision-Tower-7910:7077 --driver-memory 10g --executor-memory 10g --class GenerateTermDocMat target/scala-2.11/ocean-project_2.11-1.0.jar ../cat.json ../data_new.json ../data_new_result.json
```

### 参考资料

1. [Spark Standalone Mode (中文)](http://spark.apachecn.org/docs/cn/2.2.0/spark-standalone.html)
1. [Spark Standalone Mode](https://spark.apache.org/docs/latest/spark-standalone.html)
1. [Spark standalone cluster tutorial](http://mbonaci.github.io/mbo-spark/)
