# Spark


一、背景及问题

在谷歌提出了 MapReduce 编程模型之后，UC Berkeley 在此基础上提出了一个更加高效的模型，称为 Spark。其核心思想是将 MapReduce 的中间结果缓存到内存中，使得 Workers 可以快速读取数据而无需启动延迟极大的磁盘读取操作。这种编程模型针对一些迭代次数高、需要反复使用或者修改数据的计算任务尤其有效。根据论文的实验，Spark 在一些高迭代次数的算法实验中处理速度是 MapReduce 的 10 倍以上。

然而因为没有了将中间结果写磁盘的操作来保证容灾和恢复，因此 Spark 设计了一套别的方案来达到该目的，那就是 RDD + Lineage。RDD(Resilient Distribute Datasets)称为弹性数据集，它是一种对被操作数据的抽象，而 Lineage 称为“血统”，顾名思义它记录了每一个 RDD 演变过程中的上下文信息。当故障出现时，可以根据丢失的 RDD 的 Lineage 来追寻它的祖先 RDD ，然后重新执行演化即可恢复出丢失的 RDD。

二、Spark 编程模型

使用 Spark 的用户通过编写一个称为 Driver Program 的程序来实现自己的计算任务。Spark 为用户的并行化编程提供了三个组件：resilient distribution dataset、 parallel operations、 parallel operations。

(1) Resilient Distribution Datasets(RDDs)：一个 RDD 是一个可恢复的只读对象集合，它无须存储在磁盘中。每次对一个 RDD 的操作都会被记录下来，从而每个 RDD 可以沿着它的演化过程一直追寻它的祖先 RDD，甚至可以追寻到最开始的第一次从磁盘读取初始数据。因此这个机制保证了任何一个 RDD 都可以被恢复。具体在 Spark 系统中，RDD 由 Scala 对象来表达。论文中规定了 RDD 仅可以从四种方式构造：

 - 从文件系统中构造，例如从 HDFS 中读取数据
 - 并行化处理 Scala 对象，例如将 Scala 的数组分片
 - 从另一个 RDD 转化
 - 改变一个 RDD 的持久化状态

(2) Parallel Operations：Spark 支持的并行化操作，大体上类似于 MapReduce 的 Map 和 Reduce。有以下三类：

 - reduce
 - collect
 - foreach
 
(3) Shared Variables：Spark 支持的共享变量，在机器集群中可以传递并共享这些变量。有两类共享变量：

 - Broadcast variables：被定义为这种变量的数据会被复制到每一个 Worker，并且只复制一次。一般用于一些只读的并且需要被每个 Worker 使用的数据，例如 lookup table 等。
 - Accumulators：一般用于进行计数求和的数据。没看太懂论文中这段的解释。
 
三、编程示例

(1) Text Search：类似于 MapReduce 论文中给出的统计单词示例。这里是统计日志文件中出现 “ERROR” 的行数。

```
val file = spark.textFile("hdfs://...")
val errs = file.filter(_.contains("ERROR"))
val ones = errs.map(_ => 1)
val count = ones.reduce(_+_)
```

(2) Logistic Regression：逻辑回归学习的 Spark 并行化版本。

```
// Read points from a text file and cache them
val points = spark.textFile("hdfs://...").map(parsePoint).cache()

// Initialize w to random D-dimensional vector
var w = Vector.random(D)

// Run multiple iterations to update w
for (i <- 1 to ITERATIONS) {
    val grad = spark.accumulator(new Vector(D))
    for (p <- points) { // Runs in parallel
        val s = (1/(1+exp(-p.y*(w dot p.x)))-1)*p.y
        grad += s * p.x
    }
    w -= grad.value
}
```

(3) Alternating Least Squares：ALS 算法的 Spark 并行版本

```
val Rb = spark.broadcast(R)
for (i <- 1 to ITERATIONS) {
    U = spark.parallelize(0 until u).map(j => updateUser(j, Rb, M)).collect()
    M = spark.parallelize(0 until m).map(j => updateUser(j, Rb, U)).collect()
}
```

四、Spark 实现细节

这一节论文也没有讲得很详细，具体细节可以考虑参考工具书

 - [Spark: The Definitive Guide: Big Data Processing Made Simple][1]
 - [Learning Spark: Lightning-Fast Big Data Analysis][2]


  [1]: https://book.douban.com/subject/27035127/
  [2]: https://book.douban.com/subject/22139960/

