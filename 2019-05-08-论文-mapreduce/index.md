# MapReduce


一、背景及问题

这两天总算把谷歌三大论文之一的 MapReduce 看了。这篇论文的影响力就不多说了，谷歌AI首席科学家 Jeffrey Dean 的代表作之一。该论文主要提出了一个称为 MapReduce 的编程模型，主要是为了解决谷歌公司自身需要处理海量数据的问题。众所周知，谷歌的主要业务是搜索引擎，每天需要处理爬取到的海量文件和网页，以及对这些数据进行索引计算、处理查询请求等等。显然单台机器难以完成这样巨大的计算任务，必须使用机器集群。但如果使用专业的服务器，集群的成本就太高了。为了降低成本，谷歌采用了普通的计算机来搭建这个集群。为了实现这个目的，必须设计一套可以自动并行化计算、自我管理调度、以及拥有良好容错性的集群计算方案，因此 Jeffrey Dean 提出了这套编程模型，只要按照这个编程模型来调用接口实现计算任务，这些计算就可以自动地在一个由普通机器搭建的分布式集群上被并行地执行。

二、解决方案

具体来说，MapReduce 这个编程模型可以简单地分为 Map 部分以及 Reduce 部分。

Map 函数：由用户实现该接口，其功能是使算法输入的初始 key-value 对转化成用户定义的新 key-value 对。接下来系统会自动把它们按新 key 分组，即一个新 key 对应一组 values（key-values），并传递给 Reduce 函数。这一步类似 SQL 中的 Select GroupBy 操作。

$$key_{1}/value_{1} \implies key_{2}/value_{2} \implies key_{2}/value_{2}(s)$$

Reduce 函数：由用户实现该接口，其功能是使从 Map 函数接收过来的 key-values 组按用户定义的计算方式进行 Merge，然后输出每个 key 组的最终结果。这一步类似定义 SQL 中对被 GroupBy 的字段的聚合函数。

$$key_{2}/value_{2}(s) \implies key_{2}/value_{3} $$

论文中给出了一个具体例子，从一个巨大的文档集中统计每个单词的出现次数。每个单词可以看作 key，它的出现次数就是最终想得到的 value。按照 MapReduce 编程范式，可以分为两步，在 Map 阶段输入文档集合，为每个文档拆解出每个单词，并为每个单词赋值它的 value 为 1，表示这个单词在此出现了一次；在 Reduce 阶段，把每个单词对应的分组进行 value 的求和，即可得到每个单词的总出现次数。

Map 函数伪代码如下：
```
// key: document name
// value: document content
map(String key, String value):
    foreach word in value:
        EmitIntermediate(word,"1");
```

Reduce 函数伪代码如下：
```
// key: a word
// values: a lists of counts
reduce(String key, Iterator values):
    int results=0;
    foreach value in values:
        result += ParseInt(value);
    Emit(AsString(result));
```

论文中还提到另外一些使用了该编程模型的例子，例如

 - Distributed Grep
 - Count of URL Access Frequency
 - Reverse Web-Link Graph
 - Term-Vector per Host
 - Inverted Index
 - Distributed Sort

论文接下来就是介绍 MapReduce 系统的实现。总的来说，其架构采用 Master-Slaves 结构，Master 节点负责分配计算任务与分发数据以及监视 Workers 节点的状态。Workers 节点负责执行被分配给自己的任务，为了保证系统的容错性以及减少网络带宽消耗，Workers 的中间计算结果写入本地存储，并且尽可能由本地的 Worker 来读取。工作流程及其架构如下图所示。

![](/2019-05-08-论文-MapReduce/MapReduce.png)

其他一些具体特性如系统容错性、数据备份方案、工作任务粒度等等具体参见论文。

三、总结

论文主要针对并行处理海量数据的问题，提出了称为 MapReduce 的编程模型，只要用户将按照该范式实现 Map 和 Reduce 两个接口，该计算任务就可以自动地被一个由普通机器搭建而成的集群正确地分布式执行，而用户无需关心底层的具体执行情况。


