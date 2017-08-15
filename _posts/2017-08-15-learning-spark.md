---
layout: post
keywords: Spark 
description: Spark 学习笔记
title: "学习 Spark"
categories: [data-mining]
tags: [spark]
group: archive
icon: rocket
---
{% include mathsyouth/setup %}


### 安装 Java 及其管理工具

运行以下命令检查 Java 7 是否安装：

```
brew tap caskroom/versions
brew cask info java7
```

查看可以安装的 Java 版本：

```
brew cask search java
```

安装最新版本的 Java：

```
brew cask install java
```

运行 `java -version` 查看 Java 版本为 “1.8.0_144”。

### 下载 Spark

访问 [Spark 下载主页](http://spark.apache.org/downloads.html)，choose a Spark release of “2.2.0(July 11 2017)”, choose the package type of “Pre-built for Hadoop 2.7 and later”, and click a download type of “Direct Download”，最后点击链接 [spark-2.2.0-bin-hadoop2.7.tgz](https://d3kbcqa49mib13.cloudfront.net/spark-2.2.0-bin-hadoop2.7.tgz) 下载到本地。

打开终端，进入到下载目录下，解压缩下载的压缩文件

```shell
cd file_path
tar -xf spark-2.2.0-bin-hadoop2.7.tgz
cd spark-2.2.0-bin-hadoop2.7
```

### Spark's Python Shell

进入到 Spark 的解压缩目录下，运行如下命令打开 PySpark Shell

```
bin/pyspark
``` 

屏幕上会显示一些打印的日志，为了控制日志的输出，在 `conf` 文件夹下创建文件 `log4j.properties`，该文件夹下有文件模版 `log4j.properties.template`，复制一份并改名为 `log4j.properties`:

```
cp conf/log4j.properties.template conf/log4j.properties
vim conf/log4j.properties
```

为了降低 log level，只显示 WARN 信息，将行

```
log4j.rootCategory=INFO, console
```

更改为

```
log4j.rootCategory=WARN, console
```

当重新打开 PySpark Shell 时，显示的信息会减少。

### IPython 调用 PySpark

IPython 调用 PySpark：

```
export PYSPARK_DRIVER_PYTHON=ipython
bin/pyspark
```

Jupyter Notebook 调用 PySpark

```
export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS=notebook
bin/pyspark
```

测试安装是否成功：

```Python
# Create an RDD called lines
lines = sc.textFile("README.md")
# Count the number of items in this RDD
lines.count()
# First item in this RDD, i.e. first line of README.md
lines.first()
# Filter the lines in the file that contain a word "Python"
python_lines = lines.filter(lambda line: "Python" in line)
python_lines.first()
```

### 运行 Python 脚本

创建 Python 脚本 `my_script.py`：

```Python
from pyspark import SparkConf, SparkContext

# Initialize a SparkContext
conf = SparkConf().setMaster("local").setAppName("My App")
sc = SparkContext(conf = conf)
```

为了实现初始化，一共传入两个参数 `"local"` 和 `"My App`:

1. A *cluster URL*, namely `local` in the above example, which tells Spark how to connect to a cluster. `local` is a special value that runs Spark on one thread on the local machine, without connecting to a cluster.
1. An *application name*, namely `My App` in the above example. This will identify your application on the cluster manager’s UI if you connect to a cluster.

运行脚本：

```
bin/spark-submit my_script.py
```

### 用 RDD 进行编程

一旦 RDDs 创建成功，RDDs 提供两种操作：*transformations* 和 *actions*：

1. *transformations* 指的是依据已创建的 RDDs 构造新的 RDDs， 比如上文的例子中的 `filter` 操作；
1. *actions* 指的是基于 RDDs 计算出一个结果，比如上文例子中的 `count` 操作。

Spark 程序的工作流程如下：

1. 从外部数据创建 RDDs；
1. 利用 *transformation* (比如： `filter()`) 定义新的 RDDS；
1. 让 Spark `persist()` 今后需要反复利用的 RDDS；
1. 启动 *actions* (比如：`count()` 和 `first()`)进行并行计算。

#### 创建 RDDs

Spark 提供两种方式创建 RDDs：一种是加载外部文件；一种是并行化程序中的对象：

```Python
lines = sc.textFile("/path/to/README.md")
lines = sc.parallelize(["pandas", "i like pandas"])
```

#### RDDs 操作

*transformed* RDDs 都是 lazily 计算的，仅当要对它们进行 *action* 时，它们才被计算，从外部文件中加载生成的 RDDs 也是 lazily 生成的，所以当用户调用 `sc.textFile()`时，数据并没有被加载。 每次调用 *action* 时，整个 RDDs 需要从头开始计算。为了避免这种低效性，用户可以 `persist` 中间结果。

When you pass a function that is the member of an object, or contains references to fields in an object (e.g., `self.field`), Spark sends the entire object to worker nodes, which can be much larger than the bit of information you need.

### 参考资料

1. Holden Karau, Andy Konwinski, Patrick Wendell & Matei Zaharia, Learning Spark, 2015.
