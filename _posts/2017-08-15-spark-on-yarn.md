---
layout: post
keywords: Spark, Hadoop, YARN, HDFS
description: Spark 安装笔记
title: "在 macOS 上安装 Spark on YARN"
categories: [data-mining]
tags: [spark, hadoop, yarn, hdfs]
group: archive
icon: rocket
---
{% include mathsyouth/setup %}


### 准备工作

#### 安装 Java 及其管理工具

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

运行 `java -version` 查看 Java 版本为 “1.8.0_144”，在终端中运行以下命令找到 Java 的安装路径：

```
/usr/libexec/java_home
```

在 Mac 设置 `JAVA_HOME` 环境变量：

```
echo "export JAVA_HOME=$(/usr/libexec/java_home)" >> ~/.bash_profile
source ~/.bash_profile
```

#### 让 SSH 正常工作

打开“系统偏好设置” -> “共享” -> 勾选“远程登录”。生成 SSH Keys:

```
ssh-keygen -t rsa -P ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

确定一下命令可以运行：

```
ssh localhost
```

### 安装 Hadoop

#### 下载 Hadoop

下载最新的 Hadoop （当前为 2.7.4），Hadoop 所有的镜像都放在[Apache 官网上]( http://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-2.7.4/)，国内的可以考虑

```
wget http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.4/hadoop-2.7.4.tar.gz
```
#### 创建 Hadoop 文件夹

打开终端，进入下载 Hadoop 的文件夹（我的文件夹名为 software）：

```
cd software
tar xzvf hadoop-2.7.4.tar.gz
mv hadoop-2.7.4 /usr/local/hadoop
```

#### Hadoop 配置文件

进入 Hadoop 安装文件夹：

```
cd /usr/local/hadoop
```

指定 HDFS NameNode 的通信地址：

```
vim etc/hadoop/core-site.xml

19 <configuration>
20     <property>
21         <name>hadoop.tmp.dir</name>
22         <value>file:/usr/local/hadoop/tmp</value>
23         <description>Abase for other temporary directories.</description>
24     </property>
25     <property>
26         <name>fs.defaultFS</name>
27         <value>hdfs://localhost:9000</value>
28      </property>
29 </configuration>
```

设置 HDFS 副本数量：

```
vim etc/hadoop/hdfs-site.xml

19 <configuration>
20     <property>
21         <name>dfs.replication</name>
22         <value>1</value>
23     </property>
24     <property>
25         <name>dfs.namenode.name.dir</name>
26         <value>file:/usr/local/hadoop/tmp/dfs/name</value>
27     </property>
28     <property>
29         <name>dfs.datanode.data.dir</name>
30         <value>file:/usr/local/hadoop/tmp/dfs/data</value>
31     </property>
32 </configuration>
```

Hadoop 配置文件说明：

1. Hadoop 的运行方式是由配置文件决定的（运行 Hadoop 时会读取配置文件），因此如果需要从伪分布式模式切换回非分布式模式，需要删除 core-site.xml 中的配置项。
1. 伪分布式虽然只需要配置 `fs.defaultFS` 和 `dfs.replication` 就可以运行，不过若没有配置 `hadoop.tmp.dir` 参数，则默认使用的临时目录为 `/tmp/hadoo-hadoop`，而这个目录在重启时有可能被系统清理掉，导致必须重新执行 `format` 才行。所以我们进行了设置，同时也指定 `dfs.namenode.name.dir` 和 `dfs.datanode.data.dir`，否则在接下来的步骤中可能会出错。

接下来需要对 YARN 进行资源分配配置，否则会出现两种问题：
1. Hadoop 运行用例时在浏览器端会发现 `YarnApplicationState: ACCEPTED: waiting for AM container to be allocated, launched and register with RM.` 或者运行的 Job 程序不动。
1. 启动的 NodeManager 进程会终止，并报错：

   ```
   2017-09-07 21:52:48,233 FATAL org.apache.hadoop.yarn.server.nodemanager.NodeManager: Error starting NodeManager
   org.apache.hadoop.yarn.exceptions.YarnRuntimeException: org.apache.hadoop.yarn.exceptions.YarnRuntimeException: Recieved SHUTDOWN signal from Resourcemanager ,Registration of NodeManager failed, Message from ResourceManager: NodeManager from  XXX.XXX.XXX.XXX doesn't satisfy minimum allocations, Sending SHUTDOWN signal to the NodeManager.

   Caused by: org.apache.hadoop.yarn.exceptions.YarnRuntimeException: Recieved SHUTDOWN signal from Resourcemanager ,Registration of NodeManager failed, Message from ResourceManager: NodeManager from  XXX.XXX.XXX.XXX doesn't satisfy minimum allocations, Sending SHUTDOWN signal to the NodeManager.
   ```

Yarn 的配置文件如下：

```
vim etc/hadoop/yarn-site.xml

17 <configuration>
18     <property>
19         <name>yarn.nodemanager.aux-services</name>
20         <value>mapreduce_shuffle</value>
21     </property>
22     <property>
23         <name>yarn.nodemanager.resource.memory-mb</name>
24         <value>3072</value>
25     </property>
26     <property>
27         <name>yarn.nodemanager.resource.cpu-vcores</name>
28         <value>4</value>
29     </property>
30     <property>
31         <name>yarn.scheduler.minimum-allocation-mb</name>
32         <value>512</value>
33     </property>
34     <property>
35         <name>yarn.nodemanager.vmem-pmem-ratio</name>
36         <value>2.1</value>
37     </property>
38 </configuration>
```

参数说明：
1. `yarn.nodemanaer.resource.memory-mb` 表示该节点上 YARN 可使用的物理内存总量，默认是 8192 (MB)，如果内存资源不够 8G，则需要调小该值，因为 Yarn 不会智能的探测节点的物理内存总量。
1. `yarn.nodemanager.vmem-pmem-ratio` 任务每使用 1MB 的物理内存，最多可使用虚拟内存量，默认值是 2.1。
1. `yarn.scheduler.minimum-allocation-mb` 单个任务可申请的最少物理内存量，默认是 1024 (MB)，如果一个任务申请的物理内存少于该值，则更改该值。
1. `yarn.nodemanager.resource.cpu-vcores` 表示该节点上 YARN 可以使用的虚拟 CPU 个数，默认是 8，目前推荐将该值设置为与物理 CPU 核数数目相同。**如果节点的 CPU 核数小于 8，则需要减小该值，因为 YARN 不会智能探测节点的物理 CPU 总数，否则，会导致 NodeManager 启动后会迅速终止**。

通知 MapReduce 使用 YARN

```
vim etc/hadoop/mapred-site.xml

1 <configuration>
2     <property>
3         <name>mapreduce.framework.name</name>
4         <value>yarn</value>
5     </property>
6 </configuration>
```

#### 开启 Hadoop 服务

配置 Hadoop 环境变量

```
vim ~/.bash_profile

export HADOOP_HOME=/usr/local/hadoop
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native

source ~/.bash_profile
```

在启动运行后台程序前格式化新安装的 HDFS

```
cd /usr/local/hadoop
bin/hdfs namenode -format
```

启动 HDFS

```
sbin/start-dfs.sh
```

如果出现故障

```
localhost: namenode running as process 1942. Stop it first.
localhost: datanode running as process 2041. Stop it first.
```

然后运行

```
# 关闭 Hadoop
sbin/stop-all.sh
jps
sbin/start-dfs.sh
```

启动 YARN

```
sbin/start-yarn.sh
```

不过这里会出现一个 警告：`WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable` 这对 Hadoop 的运行没有影响。

#### 验证安装是否成功

单机模式读取的是本地数据，伪分布式读取的则是 HDFS 上的数据。要使用 HDFS，首先需要在 HDFS 中创建用户目录：

```
bin/hdfs dfs -ls /
bin/hdfs dfs -mkdir -p /user/username
```

`username` 一定要是登录用户名，我的是 `admin`，否则下面的命令会报错 mkdir: 'input': No such file or directory。接着将 `./etc/hadoop` 中的 xml 文件作为输入文件复制到分布式文件系统 `/user/username/input` 中。我们使用的是 `username` 用户，并且已创建相应的用户目录 `/user/username` ，因此在命令中就可以使用相对路径如 `input`，其对应的绝对路径就是 `/user/username/input`：

```
./bin/hdfs dfs -mkdir input
./bin/hdfs dfs -put ./etc/hadoop/*.xml input
```

复制完成后，可以通过如下命令查看文件列表：

```
./bin/hdfs dfs -ls input
```

伪分布式运行 MapReduce 作业的方式跟单机模式相同，区别在于伪分布式读取的是 HDFS 中的文件。

运行以下命令查看 Hadoop 的样例：

```
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.4.jar
```

运行 `grep` 样例：

```
./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]+'
```

在 macOS 上，MapReduce 运行在 Yarn上运行会报错：

```
17/09/08 00:26:44 INFO mapreduce.Job: Job job_1504801592198_0001 failed with state FAILED due to: Application application_1504801592198_0001 failed 2 times due to AM Container for appattempt_1504801592198_0001_000002 exited with  exitCode: 127

Container exited with a non-zero exit code 127
```

解决方法是：Hard coding 文件 `etc/hadoop/hadoop-env.sh` 中 Java Home path

```
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_144.jdk/Contents/Home
```

查看位于 HDFS 中的输出结果：

```
./bin/hdfs dfs -cat output/*
```

Hadoop 运行程序时，输出目录不能存在，否则会提示错误 `org.apache.hadoop.mapred.FileAlreadyExistsException: Output directory hdfs://localhost:9000/user/hadoop/output already exists`，因此若要再次执行，需要执行如下命令删除 output 文件夹:

```
./bin/hdfs dfs -rm -r output
```

可以通过登录 Web 端进行监控：

1. HDFS Administrator: http://localhost:50070/
1. JobTracker: http://localhost:8088

#### HDFS shell 常用操作

1. 帮助命令

   ```
   bin/hadoop fs -help
   ```
1. 上传

   ```
   bin/hadoop fs -put <linux上文件>  <hdfs上的路径>
   ```
1. 查看文件列表

   ```
   bin/hadoop fs -ls /
   ```
1. 下载文件

   ```
   bin/hadoop fs -get <hdfs上的路径>  <linux上文件>
   ```

### 安装 Spark

#### 下载 Spark

访问 [Spark 下载主页](http://spark.apache.org/downloads.html)，choose a Spark release of “2.2.0(July 11 2017)”, choose the package type of “Pre-built for Hadoop 2.7 and later”, and click a download type of “Direct Download”，最后点击链接 [spark-2.2.0-bin-hadoop2.7.tgz](https://d3kbcqa49mib13.cloudfront.net/spark-2.2.0-bin-hadoop2.7.tgz) 下载到本地。

打开终端，进入到下载目录下，解压缩下载的压缩文件

```shell
cd file_path
tar -xf spark-2.2.0-bin-hadoop2.7.tgz
mv spark-2.2.0-bin-hadoop2.7 /usr/local/spark
cd /usr/local/spark
```

将 Spark bin 文件加到 PATH 中

```
vim ~/.bash_profile
# 将本行加到文件末
export PATH=$PATH:/usr/local/spark/bin

source ~/.bash_profile
```

#### 安装 Scala

如果安装了 Homebrew，则直接运行

```
brew update
brew install scala
```

然后，检查安装的 Scala 版本

```
scala -version
brew info scala scala
```

如果没有安装 Homebrew, 则首先现在 Scala

```
wget https://downloads.lightbend.com/scala/2.12.3/scala-2.12.3.tgz
tar xzvf scala-2.12.3.tgz
mv scala-2.12.3 /usr/local/scala
```

将 Scala bin 文件夹加到 PATH 中

```
vim ~/.bash_profile
# 将本行添加到文件末
export PATH=$PATH:/usr/local/scala/bin

source ~/.bash_profile
```

打开 Spark Shell

```
spark-shell
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

在本地运行应用：

```
bin/spark-submit --master local[*] examples/src/main/python/pi.py 100
```

要在 YARN cluster 上运行应用，首先配置 `HADOOP_CONF_DIR` 环境变量：

```
vim ~/.bash_profile
# 加在文件末
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop

source ~/.bash_profile
```

然后运行：
```
./bin/spark-submit --master yarn --deploy-mode cluster --driver-memory 1g --executor-memory 1g --executor-cores 1 examples/src/main/python/pi.py 100
```

### 用 RDD 进行编程

在 Spark 2.0 以前，Spark 的主要编程接口是 Resilient Distributed Dataset (RDD)，Spark 2.0 之后，RDDs 被Dataset 所替代，Dataset 和 RDD 很相似，但是性能会更好。关于 Dataset 的信息，可以参考[Spark SQL, DataFrames and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)。这里简单回顾一下 RDD，后续会详细介绍 Dataset。一旦 RDDs 创建成功，RDDs 提供两种操作：*transformations* 和 *actions*：

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

### 补充：在 Ubuntu 16.04 上安装 Spark

这里仅指出在 Ubuntu 16.04 上安装步骤的不同之处，其他相同步骤请参考上文。

在终端上运行命令 `java -version`，如果显示 "请尝试：sudo apt install <选定的软件包>"，运行命令：

```
sudo apt-get update
sudo apt-get install openjdk-8-jre-headless -y
sudo apt-get install scala
```

注意此处不需要将 `JAVA_HOME` 环境变量写入到 `.bash_profile` 文件中。

### 参考资料

1. Holden Karau, Andy Konwinski, Patrick Wendell & Matei Zaharia, Learning Spark, 2015.
1. [INSTALL HADOOP AND SPARK ON A MAC](https://geekatwork.wordpress.com/2016/04/28/install-hadoop-and-spark-on-a-mac/)
1. [Hadoop安装教程_单机/伪分布式配置_Hadoop2.6.0/Ubuntu14.04](http://www.powerxing.com/install-hadoop/)
1. [HOW TO PLAN AND CONFIGURE YARN AND MAPREDUCE 2 IN HDP 2.0](https://hortonworks.com/blog/how-to-plan-and-configure-yarn-in-hdp-2-0/)
