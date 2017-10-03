---
layout: post
keywords: Spark, Hadoop, YARN, HDFS
description: Spark 2.2 学习笔记
title: "Spark 2.2 学习笔记"
categories: [data-mining]
tags: [spark, hadoop, yarn, hdfs]
group: archive
icon: rocket
---
{% include mathsyouth/setup %}


### PySpark Shell

#### 配置 Pypark Shell

为了方便启动 PySpark Shell，创建新的脚本名为 `~/.ipython_profile`:

```
vim ~/.ipython_profile

export PYSPARK_DRIVER_PYTHON=ipython
export PATH=$PATH:/usr/local/spark/bin
pyspark

source ~/.ipython_profile
```

也可以通过 Jupyter Notebook 启动 PySpark shell, 创建名为 `~/.jupyter_profile`：

```
vim ~/.jupyter_profile

export PATH=$PATH:/usr/local/spark/bin
export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS=notebook
pyspark

source ~/.jupyter_profile
```

#### 入门

Spark 的主要抽象是分布式的 item 集合，被称之为 Dataset。Datasets 的创建既可以通过 Hadoop 的输入文件系统 -- HDFS，也可以通过其他 Datasets 进行转换而来。在 Python 中，所有的 Datasets 都是 Dataset[Row]，被称之为 DataFrame，这和 Pandas 中的概念一直。首先，我们通过 Spark 源文件夹中的 RAEDME 文件来创建一个新的 DataFrame:

```python
textFile = spark.read.text("file:///usr/local/spark/README.md")
```

对 DataFrame 进行基本操作

```python
# Number of rows in this DataFrame
textFile.count()
# First row in this DataFrame
textFile.first()
# 过滤出包含 "Spark" 字符的行，并形成新的 DataFrame
linesWithSpark = textFile.filter(textFile["value"].contains("Spark"))
# 计算包含 "Spark" 的行数
textFile.filter(textFile.value.contains("Spark")).count()
```

对 DataFrame 进行复杂的操作：

```python
from pyspark.sql.functions import *

# 找到有最多单词的行
textFile.select(size(split(textFile.value, "\s+")).name("numWords")).agg(max(col("numWords"))).collect()
```

This first maps a line to an integer value and aliases it as “numWords”, creating a new DataFrame. `agg` is called on that DataFrame to find the largest word count. The arguments to `select` and `agg` are both `Column`, we can use `df.colName` to get a column from a DataFrame. We can also `import pyspark.sql.functions`, which provides a lot of convenient functions to build a new `Column` from an old one.

Spark 可以很容易地实现 MapReduce 流：

```
wordCounts = textFile.select(explode(split(textFile.value, "\s+")).alias("word")).groupBy("word").count()
wordCounts.collect()
```
Here, we use the `explode` function in `select`, to transfrom a Dataset of lines to a Dataset of `words`, and then combine `groupBy` and `count` to compute the per-word counts in the file as a DataFrame of 2 columns: “word” and “count”. Then call `collect` to collect the word counts in the shell。 **[官方的代码][Spark Quick Start] 为 `explode(split(textFile.value, "\s+")).as("word")` 有语法错误，应将 `as` 改为 `name`。**

当 Datasets 需要反复被访问时，比如，经常查询一部分的 Datasets 或者运行迭代算法，我们可以对其进行缓存：

```
linesWithSpark.cache()
```

有时候我们需要利用 Spark API 编写一个完备的应用，比如，我们编写一个简单的 Spark application `SimpleApp.py`:

```python
"""SimpleApp.py"""
from pyspark.sql import SparkSession

# Should be some file on your system
logFile = "file:///usr/local/spark/README.md"
spark = SparkSession.builder.appName("SimpleApp").master("local[*]").getOrCreate()
logData = spark.read.text(logFile).cache()

numAs = logData.filter(logData.value.contains('a')).count()
numBs = logData.filter(logData.value.contains('b')).count()

print("Lines with a: %i, lines with b: %i" % (numAs, numBs))

spark.stop()
```

我们可以通过 `bin/spark-submit` 运行该 application：

```python
./bin/spark-submit simple_app.py
```

#### Spark 初始化

PySpark 要求在 driver 和 worker 上的 Python 版本一致，它使用在环境变量 `PATH` 中默认的 Python 版本，用户可以自己通过设置环境变量 `PYSPARK_PYTHON` 来指定特定的 Python 版本：

```
PYSPARK_PYTHON=python3.4 bin/pyspark
PYSPARK_PYTHON=/opt/pypy-2.5/bin/pypy bin/spark-submit examples/src/main/python/pi.py
```

一个 Spark 程序首先需要创建一个 `SparkContext` 对象用来告诉 Spark 如何访问集群，为了创建这一对象，首先需要构建一个 `SparkConf` 对象，该对象中包含了应用所需要的信息：

```Python
conf = SparkConf().setAppName(appName).setMaster(master)
sc = SparkContext(conf=conf)
```

`appName` 参数是指定应用的名称，它会在集群的 UI 上显示出来，`master` 参数是一个 [Spark, Mesos 或者 YARN 集群 URL, 或者特定的 “local” 字符串](https://spark.apache.org/docs/latest/submitting-applications.html#master-urls) 用来指定本地机器运行。在实际中，如果代码要在集群上运行，不需要在代码中 hardcode 参数 `master`，而是[在用 `spark-submit` 启动应用时在指定该参数](https://spark.apache.org/docs/latest/submitting-applications.html)，但对于本地测试和单元测试而言，可以传递 `local` 参数来运行 Spark。

在 PySpark shell 中，一个编译器感知的 `SparkContext` 对象 `sc` 已经被创建好了，用户自定义的 `SparkContext` 将不起作用，运行 `pyspark --help` 可以查看所有的可选参数。


### Resilient Distributed Datasets (RDDs)

#### 创建 RDDs

主要有两种方式来创建 RDDs：一种是并行化在 driver 程序中已经存在的 collections，另一种方式是从外部存储设备中加载数据，比如，本地文件或者 HDFS。

首先，通过调用 `SparkContext` 的 `parallelize` 方法作用到已经存在的 collections 上来创建可并行化的 collections。比如，创建一个可并行化的 collection 包含数字1到5:

```Python
data = [1, 2, 3, 4, 5]
distData = sc.parallelize(data)
```

对于可并行化的 collections 而言，一个重要的参数是把数据切分成多少份，Spark 会对每一个 partition 运行一个 task，一般来说，给每一个 CPU 分配2-4个 partitions。尽管 Spark 会根据集群自动分配 partitions 数，但是可以根据实际情况来手动设置该变量，比如，`sc.parallelize(data, 10))`。

其次，通过调用 `SparkContext` 的 `textFile` 方法来创建文本文件 RDDs，这一方法读取文件的 `URI` (比如，机器本地文件或者 `hdfs://`、 `s3n://` 等等 URI) 创建一个行的 collection：

```Python
distFile = sc.textFile("data.txt")
```

如果采用本地文件系统的路径，该文件必须在相同的路径下被 worker 节点访问到，要么将该文件复制到所有的 workers 上，要么采用网络共享的文件系统。所有的 Spark 基于文件的输入方法（包括 `textFile`) 支持运行文件夹、压缩文件和 wildcards 等，比如：`textFile("/my/directory")`、 `textFile("/my/directory/*.txt")` 和  `textFile("/my/directory/*.gz")`。`textFile` 方法也可以接受可选参数用来控制文件的 partition 数，默认的情况下，Spark 会为文件的每一个 block 创建一个 partition（在 HDFS 中每个 block 的大小为 128MB），但是可以设置更多的 partitions，需要注意的是：partition 数一定要大于等于 block 数。除此之外，`SparkContext.wholeTextFiles` 可以读取一个文件夹中包含的多个小文件，并将每一个文件返回为 `(filename, content)` 对，这和 `textFile` 不同，因为它返回的是每个文件每一行的记录。

#### RDD 操作

默认情况下，每次对一个变换而来的 RDD 进行 action 操作时，该 RDD 可能需要反复被计算。但是，可以通过调用 `persit` 或者 `cache` 方法将该 RDD 保存在内存中，在该情况下 Spark 下次可以更快地访问到它。Spark 同时也支持将 RDD 保存在硬盘上或者在多个节点之间进行复制。

当在集群上运行代码时，理解变量和方法的范围和生命周期是一件较难的事情，比如，下面的代码调用 `foreach()` 来增加计数器：

```Python
counter = 0
rdd = sc.parallelize(data)

# Wrong: Don't do this!!
def increment_counter(x):
    global counter
    counter += x
rdd.foreach(increment_counter)

print("Counter value: ", counter)
```

在执行 task 之前，Spark 会计算任务的闭包（闭包就是 executor 可见的变量和方法，这里指的是 `foreach()`）。在闭包中的变量副本会被传递给 executor，因此，当 `counter` 在 `foreach` 方法中被引用时，它已经不是 driver 节点上的 `counter`，尽管在 driver 节点内存里有 `counter`，但是它对于 executors 而言不可见，因为 executors 只能看到序列化后的闭包的副本。既然所有对 `counter` 的操作都是引用序列化的闭包的值，导致最终的 `counter` 依然等于 0。

为了打印 driver 上的所有元素，可以首先用 `collect()` 方法将 executors 上的 RDD 汇聚到 driver 节点上： `rdd.collect().foreach(println)`。但是这样做有可能导致 driver 节点的内存溢出，如果只是打印 RDD 的几个元素，一个更加安全的方法是调用 `take()` 方法： `rdd.take(100).foreach(println)`。

#### Shuffle 操作

在 Spark 中，数据一般会分布式存储在不同的 partitions 上，在计算的过程中，一个 task 会对一个 partition 进行操作，因此，为了执行一次 `reduceByKey` reduce task，Spark 需要组织所有的数据并执行 all-to-all 的操作，它必须从所有的 partitions 中找到所有 keys 的所有相对应的值，然后将跨 partitions 的值聚合在一起并最终为每一个 key 计算最终的结果 - 这一过程称之为 shuffle。导致 shuffle 的操作包括： repartition 操作 -- `repartition` 和 `coalesce`、 *ByKey* 操作 (除了 `count` 之外) -- `groupByKey` 和 `reduceByKey`、 *join* 操作 -- `cogroup` 和 `join`。

Shuffle 是计算量很大的操作，因为它涉及到硬盘的 I/O，数据的序列化以及网络 I/O。Spark 为了组织数据进行 shuffle 操作，它需要生成一系列的 map tasks 进行组织数据，并生成一系列的 reduce tasks 来聚合数据。 map tasks 的结果都存到内存里，只到内存存满为止，然后根据目标的 partition 进行排序并写到单个文件中；对于 reduce tasks 而言，它们读取相关的排序块。当内存存满之后， Spark 将会把这些表存到硬盘里，这样会导致额外的硬盘 I/O 开销和增加的垃圾回收。Spark 也会在硬盘上产生大量的中间文件，这些文件会一致保留在硬盘上直到相应的 RDDs 不再被利用和被垃圾回收。如果 application 保留这些 RDDs 的引用或者垃圾回收不会频繁进行，那么垃圾回收可能会在一段长时间之后发生，也就是说，长期运行的 Spark jobs 会消耗大量的硬盘空间，当在配置 Spark context 时，零时的存储文件可以由 `spark.local.dir` 配置参数进行指定。

#### RDD 持久化

Spark 一个重要能力就是在内存中持久化或者缓存数据。当持久化 RDD 时，每一个节点都会每一个 partition，当再次在其他 actions 中需要它们的时候，数据可以直接利用而不需要重新计算，这可以让后续的 actions 的速度提升很多（通常都是10倍以上）。可以用 `persist()` 或 `cache()` 方法持久化数据，Spark 的 `cache` 是容错的 -- 如果任意的 partition 丢失了，Spark 会自动根据变换重新计算出这些丢失的 partition。除此之外，每一个持久化的 RDD 能够通过不同的存储级别，比如，可以在硬盘上持久化，可以在内存里持久化但是为了节省空间必须是序列化的 Java 对象，也可以在不同的节点上复制。存储级别可以通过传入 `StorageLevel` 对象（Scala, Java, Python) 给 `persist()`来实现，`cache()` 方法是默认存储级别的简写，即 `StorageLevel.MEMORY_ONLY`。

Spark 的存储级别是为了提供内存使用和 CPU 效率之间的权衡，推荐按以下顺序进行选择：
1. 如果 RDDs 可以全部存储到内存中，选择 `MEMORY_ONLY`。这种是 CPU 效率最高的选择，允许 RDDs 上的操作尽量块；
2. 如果内存不够的化，选择 `MEMORY_ONLY_SER` 同时选择一个快的序列化库使得对象的序列化尽量节省空间，但同时保证对象能够快速的被访问（目前仅支持 Java 和 Scala)；
3. 不要将数据存储到硬盘上，除非计算数据的函数计算量很大，或者需要过滤掉大量的数据。否则，重新计算一个 partition 可能和从硬盘上读取数据的速度一样块；
4. 如果想要快速的进行故障恢复的话，采用备份的存储策略。虽然所有的存储级别都提供了通过重新计算丢失数据的完整容错方案，但是备份的数据可以让你持续地对这些 RDD 进行跑 tasks，而不需要等待重新计算丢失的 partition。

#### 删除数据

Spark 会自动监视每一个节点上缓存的使用情况，并采用最近使用的方式 (least-recently-used) 删除旧的数据 partitions。如果你想手动删除 RDD 而不是等待它从缓存中被删除，可以使用 `RDD.unpersist()` 方法。


### 共享变量

通常来说，当传递给一个 Spark 操作（比如：`map` 或 `reduce`）的函数在远程的集群节点上执行时，该函数中所有的变量都是独立的副本，这些变量被复制到每一台机器上，在远程机器上的变量更改不会重新传给 driver 程序，因为一般而言跨 tasks 的读写共享变量效率很低。但是 Spark 确实提供两种类型的共享变量：广播变量和累加器。

#### 广播变量

广播变量允许程序员将一个只读的变量缓存在每一台机器上，而不要和 tasks 一起传输它的副本，比如，广播变量可以被用来给每一个节点上有效地传输大量输入数据的副本。Spark 试图采用有效的广播算法来减小分布广播变量的通信开销。Spark 的 actions 是通过执行一系列的 stages (由分布式的 shuffle 操作来定义) 来完成，Spark 会自动广播在每一个 stage 中 tasks 所需要的数据，这些广播的数据都以序列化的形式缓存起来，然后在运行每一个 task 之前进行反序列化。也就是说，仅当跨多个 stages 的任务需要相同的数据或者以反序列化形式缓存的数据很重要时，显示创建广播变量才是有用的。广播变量可以通过调用 `SparkContext.broadcast(v)` 来创建，其中 `v` 是一个变量：

```Python
broadcastVar = sc.broadcast([1, 2, 3])
broadcastVar.value
```

一旦广播变量被创建了，在任意函数中 `v` 的值不应该在集群上运行，因为 `v` 不应该被运输两次。除此之外，`v` 对象在被广播之后应该被修改，这样能够确保所有的节点能够获得广播变量相同的值。

#### 累加器

累加器是通过结合律和交换律操作进行加法的变量，因此可以有效的支持并行。它可以用来实现 MapReduce 中的计数器或者求和运算。Spark 天生支持数值类型的累加器，程序员也可以增加对新类型的支持。累加器可以通过调用 `SparkContext.accumulator(v)` 来创建，其中 `v` 是一个初始值。在集群上的 task 可以通过 `add` 方法或者 `+=` 操作对它进行加法。但是，这些任务都不可以读它的值，只有 driver 程序可以读取累加器的值：

```Python
accum = sc.accumulator(0)
sc.parallelize([1, 2, 3, 4]).foreach(lambda x: accum.add(x))
accum.value
```

累加器的更新只可以在 actions 内部进行，Spark 确保每一个任务对累加器的更新只会进行一次，也就是说重启的任务不会更新该值。在 transformatins 中，用户应该小心每一个任务的更新会执行两次，如果 tasks 或者 job stags 重新执行的话。因此，累加器的更新不确保在 transformatin 中会发生，下面的代码说明了这一性质：

```Python
accum = sc.accumulator(0)
def g(x):
    accum.add(x)
    return f(x)
data.map(g)
# Here, accum is still 0 because no actions have caused the `map` to be computed.
```


### Datasets and DataFrames

Dataset  是在 Spark 1.6 版本中新增的一种接口，它提供了 RDDs 的特性（强类型检验、能够运用 lambda 函数），同时拥有 Spark SQL 优化的执行引擎的优点。 Dataset 能够从 JVM 对象中构造，然后进行函数是变换 (`map`、`flatMap`、`filter` 等)。Scala 和 Java 提供 Dataset API，Python 不提供，但是由于 Python 的动态性质，Dataset API 的优点已经存在于 Python 中（比如说，可以通过行的名字 `row.columnName` 访问数据）。

DataFrame 是按列名组织的 Dataset，它在概念上等价于关系型数据库中的一张表或者 Pyton 中的 data frame，但是幕后有更加丰富的优化。DataFrames 可以通过不同的方式构造：结构的数据文件，Hive 中的表，外部数据库或者已存在的 RDDs。 在 Scala 和 Java 中，DataFrame 是由 DataSet 的行表示，在 Scala 中， DataFrame 只是 `Dataset[Row]` 的别名，而在 Java 中，用户可以用 `Dataset<Row>` 表示 DataFrame。

Spark 中所有函数的入口是 `SparkSession` 类，为了创建一个基本的 `SparkSession`，采用：

```Python
from pyspark.sql import SparkSession
spark = SparkSession \
    .builder \
    .appName("Python Spark SQL basic example") \
    .config("spark.some.config.option", "some-value") \
    .getOrCreate()
```

### 数据源

Spark SQL 通过 DataFrame 的接口支持对不同的数据源进行操作。数据源可以由它们的全名来指定（比如：`org.apache.spark.sql.parquet`），但是对于内置的数据源可以使用它们的简写（`json`、`parquet`、`jdbc`、`orc`、`libsvm`、`csv`、`text`）。由某种类型的数据源加载而来的 DataFrames 可以转换成其他类型：

```Python
df = spark.read.load("examples/src/main/resources/people.json", format="json")
df.select("name", "age").write.save("namesAndAges.parquet", format="parquet")
```


### 参考资料

1. [Spark Quick Start](https://spark.apache.org/docs/latest/index.html): a quick introduction to the Spark API.
1. [Spark Programming Guide](https://spark.apache.org/docs/latest/rdd-programming-guide.html#spark-programming-guide)
1. [Spark SQL, DataFrames and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)
