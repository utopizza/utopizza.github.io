# 京东面经


一面（2018-08-01 晚上8点，50分钟）

1. 自我介绍

2. 讲一下在导师公司做的项目，有什么技术难点。说了数据库的用版本控制实现一个乐观锁。然后问了下工业AI比赛，简单讲了下思路（分析工序间的联系，筛选数据，然后预测）

3. jvm：内存模型、垃圾回收算法、垃圾收集器（没答好）；有什么类加载器（没答好）、双亲委派模型、有什么优缺点（没答好）；jvm如何调优（不会）

4. java：HashMap的内部机制、初始大小与扩容、为什么选择这个数值（没答好，给了答案是保持2的幂作性能开销小？）、为什么要用拉链法（我答的是为了解决碰撞问题。如果用线性探测开放地址法，每次删除一个元素，后面的所有碰撞的元素全部都要重新散列，代价非常大）、是否线程安全、哪个是线程安全、HashTable和ConcurrentHashMap如何实现线程安全、ConcurrentHashMap的内部机制、分段锁的思想。HashMap1.7之后有什么变化？红黑树。为什么？加快查询速度。

5. 分布式了解吗，场景题：假设有100台服务器，争抢一个任务，应该怎么做？不太明白什么意思，反问是什么任务，是不是客户端请求，然后把反向代理和均衡负载说了下，面试官说不是，然后我又讲了paxos协议，他说也不是，实在想不出来，他说是用锁。。？没细说

6. spring：ioc，aop，能有多详细讲多详细

7. springMVC如何分析请求，然后如何交给handler处理，能有多详细讲多详细（不会）

8. mybatis怎么用（没答好），除了mapper还有什么（不会），对比hibernate有什么优点（不会）

9. redis讲一下，有什么应用场景（答得不好，答了说存session，面试官说不是，然后我说缓存？面试官好像没说啥），与mencache对比有什么优缺点

10. 场景题，有一些的jvm参数的历史数据（例如gc次数，gc时间等等），如何预测jvm是否会发生故障（特别是往年的京东618时候的jvm参数），说思路。我想了好久，面试官一直降低难度甚至换题目，我实在想不出，就随便后来说了下，先分析出都有哪些jvm的影响因素，然后用历史数据训练得到每个因素的权重，然后就可以使用这些权重来预测是否发生故障。面试官说这不就是我刚开始想问的嘛，笑了笑说让等二面

二面（2018-08-02下午五点半，约10分钟）

1. 自我介绍

2. 简单讲一下项目

3. 什么框架使用最熟悉，会不会HQL

4、你是如何分配机器学习和java研发的精力

5. 如何利用机器学习预测jvm的故障，和一面的问题差不多，我乱扯了一些

6. 说一下你最强势或者突出的技术特长，（我直接承认框架使用比较少，因为接触的项目不多，但是我对jdk、jvm、计算机基础比较扎实，也比较感兴趣，还以为他要开始狂问，但结果没问）

7. 能不能来实习（他表示，不能来实习就很难办了）

8. 简单介绍了他的部门

感觉像是hr？没怎么问技术，听声音好像年纪比一面的面试官应该大不少，还问华科是不是华工。。

三面（8月8日下午6点，约21分钟）

1. 自我介绍

2. n个已排序队列，选出队列中最大的top k个数。显然先对每个队列取top k，然后再选出其中的top k。说了用小根堆，(nk)logk。面试官问还有没有更快的，没答上。后来和同学讨论了下，最好应该分情况答，如果n远大于k，应该用归并，复杂度是 klog(n)。如果k远大于n，则应该用小根堆。

3. 讲一下阿里天池无人机的那个比赛，是怎么做的。对深度学习，图像处理有没有了解，答没有。

4. 讲一下职业规划

5. 有什么问题


四面（8月9日下午7点，感觉也不算面试，就是做了道题）

面试官给了链接，一道自他己出的小题目，对一个文件的数据进行求和统计然后排序输出，大概十五分钟写完，然后面试官不见了，不知道啥情况，等了一个多小时后我自己下线了

HR面（8月10日中午2点，约7分钟）

1. 简单介绍下项目

2. 一边看我简历一边说，保研成绩啊，比赛名次啊，一直在夸我都不好意思了

3. 家在哪里，北京有没有亲戚

4. 对京东了解吗？我笑了笑说在中国还有谁不了解京东的吗，然后她也笑了。然后我就说买东西基本都是京东买什么的。

5. 说大概一周之后会答复我，走后面的流程什么的

6. 有什么要问的

