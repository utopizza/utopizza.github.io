# Kafka


一、背景问题

在互联网公司中往往会产生大量的日志数据（log data），例如（1）用户事件如用户的登录、网页访问、点击、收藏、分享、评论、搜索；（2）内部系统产生的事件例如服务调用、错误日志、网络事件、系统调用等等。这些日志数据十分重要，可用于分析用户喜好、系统使用情况等等。以往这些日志数据一般用于离线的数据分析，然而随着互联网业务蓬勃的发展，越来越需要更加实时地利用这些日志数据来进行在线分析，例如（1）根据用户搜索历史进行实时更新的个性化推荐、相关推荐、广告投放（2）系统实时识别垃圾邮件、非法数据并自动进行过滤等等。因此，一些早期的系统，例如Facebook的Scribe，Yahoo的Data Highway，Cloudera的Flume等等，都是被设计成把日志数据进行收集并写入Hadoop这样的数据仓库然后进行离线的分析，不能满足进行实时大规模日志在线分析的需求。业界需要一个能支持延迟秒级的实时（real-time）日志收集系统。

二、Kafka

1、基本概念&架构

- 一个message流被称为一个topic，每个topic下有多个partition分区（分区数量十分重要，因为它决定了可以并行消费的并行度，见下文）
- 存储message的kafka节点称为broker。Kafka是一个分布式系统，有多个broker节点，每个broker节点存储一个或者多个partition分区
- 每个producer可以推送message到一个topic
- 每个consumer可以订阅一个或者多个topic，从对应的broker中拉取message

![](/2020-08-04-论文-kafka/structure.png)

2、Efficiency

（1）简单的存储结构

每个topic的每个partition分区对应一个逻辑日志，该日志物理上其实是由一系列的文件片（segment file）构成，每个文件片大约1GB。每当producer推送一个message到某个partition时，broker只是简单地把该message追加到最后一个片文件的末尾。考虑到写磁盘的性能问题，kafka只有在指定数量的message到达、或者指定时间周期结束才执行一次flush把文件片写入磁盘。

至于message的数据结构，与传统message系统不同，kafka中的message并没有所谓的“messageId”，每个message只根据它在日志中的offset来定位：下一条message在日志中的位置等于当前message的位置+message的长度。

consumer从一个partition消费message时，它只能从某个offset开始连续地消费，并且该offset之前的message已经完成消费。consumer在向broker提交每个的pull请求中都会带上所请求的message的offset以及请求的字节数。broker根据offset找到对应的文件片（会在内存中维护一个“文件片首部offset”与“文件片”的映射关系），定位message，然后发送被请求的数据返回给consumer。consumer收到数据后，计算出下一个message的offset然后再继续发送请求。

 ![](/2020-08-04-论文-kafka/log.png)

（2）高效的传输方式

producer可以在单个send请求中一次性提交多个message；同样，consumer也可以在单个pull请求中一次性拉取多个message。

另外，kafka为了防止数据在机器上被缓冲两次（double buffering），它不在内存中缓存任何数据，所有数据只利用了操作系统的页缓冲（page cache，应该就是操作系统底层为写磁盘提供的内核缓冲区）。这样做的好处，文中解析是基本不用考虑内存的垃圾回收，并且在kafka进程重启的时候可以很方便的从页缓冲中重新加载数据（如果是维护在内存中，进程吃消失后分配的内存会被回收释放）

更进一步，kafka对网络传输也做了优化。传统的程序把文件数据发送到网络中一般需要以下几个步骤：

- 从磁盘或者其他存储媒体把文件数据读到操作系统内核的缓冲区（page cache）
- 从内核缓冲区把数据拷贝到应用程序中的buffer
- 把数据从应用程序中的buffer又重新拷贝到内核中的另一个缓冲区（可能是为网卡分配的缓冲）
- 把内核缓冲区中的数据发送到socket套接字层，进行网络传输

可见上述过程经历了4次拷贝：文件-》内核缓冲-》应用程序缓冲-》内核缓冲-》socket。然而在Linux或者Unix类的操作系统中，其实有一个名为“sendfile”的API可以直接把文件读到一个内核缓冲后直接发送到socket，kafka利用了该API进行高效的文件数据发送：文件-》内核缓冲-》socket。

（3）无状态broker

在kafka中，每个consumer消费到哪个offset位置并不会被kafka记录，而是由consumer自己进行记录。这样设计的好处是极大了简化了kafka系统的复杂度。但是这样会引入另一个问题：kafka无法知道一个message是否已经被所有的consumer消费过，是否可以进行删除。kafka使用了一个简单解决方案——每个message保留7天，超过该时间的message会被删除。这样设计的好处一是简洁，二是有一定的容灾能力，例如consumer崩溃或者出现一些故障后，可以减小offset从头消费，重新获取消费错误或丢失的数据。

3、分布式协作

在kafka中，可以定义一个consumer组（consumer group），每个这样的组消费一个指定的topic，该topic下的每个partition只能被组中的一个consumer消费，该consumer可以是一个线程或者进程。这样设计的好处是足够简单，因为如果有多个consumer同时消费一个partition，势必需要进行同步来保证message不会被一个组重复消费，可能需要引入锁，这样会使得消费效率大打折扣。因为规定了组内一个consumer只能消费一个partition，因此组内的consumer同步只会出现在进行rebalance的时候，而这是一个出现频率很低的场景。

对于kafka节点结构，它并没有设计成master-slave结构，而是每个broker均为同等地位的数据节点，但是会把一些重要的集群信息存放到Zookeeper中。Zookeeper在kafka中负责的任务主要有：

- 监测broker、consumer的加入和离开
- 触发一次rebalance，当上一点发生的时候
- 记录consumer与partition对应关系，追踪每个partition的offset

4、交付保证

kafka只保证“至少一次”的数据交付（at-least-once）。如果外部应用程序不能引入重复数据，则它需要自行实现去重逻辑（deduplicated logic）。kafka只保证单个partition内的message会被按顺序交付给consumer，并不会保证不同partition之间的message的交付顺序。
