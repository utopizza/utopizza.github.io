# 阶段总结(6)


2018年6、7、8、9月回顾

从7月开始，一共面了顺丰、大疆、京东、网易、美团、华为、百度、腾讯这八家公司，也都拿到了offer。阿里、头条没有去面，因为听闻加班太严重而且离家太远了（当时不知道头条在深圳有分公司），就没投。自9月30号面完最后一家腾讯后，就不继续面其他公司了，回家休息了两周（10月1号至14号）。10月15号回到学校，继续做实验室老板安排的项目任务。

由于春招的时候完全没有时间准备，没有投实习，没有拿到实习offer保底，实验室也不放人出去实习，因此5月份开始复习看书的时候，心里没底，很担心秋招找不好工作。同时还感觉有很多书要看，很多知识要补，相当焦虑紧张。但这样也有好处，那就是焦虑和紧张的状态会逼迫自己不敢有一丝松懈，感觉5月份到7月份这三个月看书复习的效率相当高。现在回过头来看自己都感到惊讶，仅仅三个月，从接近零基础，到面八家互联网公司就拿八个offer，没有被刷过一次。

现在简单回顾一下从5月份到7月份的三个月的复习，以及8月份到9月份两个月的面试。

一、算法

算法没啥好说的，必须把基础算法吃透，其实不多，也就排序、搜索、数据结构、树、图这几大类。当时是把橙色那本《算法第四版》上面的所有代码手敲了两遍，然后再尝试自己写一遍。因为觉得那本书上面的代码写得实在太漂亮了，相当的简洁，相当符合我的个人审美，并且那本书的作者是个巨神。

这样搞了三遍基础的书法和数据结构后，心里就有些底，没那么方了。接下来也没啥好说的，刷题。当时主要是在leetcode刷，因为讨论区相当活跃，每道题的讨论区有太多让人五体投地的巧妙解法了。刷leetcode之外，配合《剑指offer》和左程云的《程序员面试指南》，应该够了。leetcode前100道刷了两三遍，100到200题没时间了，应该是只做了一遍。剑指offer最重要，一定要烂熟，可以去牛客网刷，刷了3遍左右，时不时要复习，因为面试题几乎全部是从上面出，原题。左程云那本只看了一遍，手敲了一遍，偶而回过头看看代码。

二、计算机基础

这里的计算机基础指的是计算机网络、操作系统、数据库。对于这三科，当时的复习策略是：

1. 先使用本科学过的课本快速回顾基础知识，因为学过的课本熟悉感最强，这样快速回顾一遍基础知识点可以一定程度的减缓焦虑，稳定情绪。当时用两周复习了本科学过的计算机网络、数据库、操作系统。
2. 然后开始啃对应的黑皮书，在一些关键点上看深入去，因为单靠本科课本那点深度是完全不够应付面试官的。当时计算机网络用《计算机网络：自顶向下方法(6th)》，数据库用《数据库系统概念(6th)》，操作系统用《现代操作系统(4th)》。还有其他的一些黑皮书，在阶段总结(5)中可以有介绍。
3. 啃完黑皮书后，理论基础应该差不多了，然后就找到对应的实现软件来研究，这个时候看起书来就相当轻松了。计算机网络就深入看一些博客，或者针对常问的TCP/UDP协议深入去看；数据库找了MySQL和Redis的书来看，如《Mysql InnoDB》和《Redis开发与运维》；操作系统找了一些Linux编程的入门书来看，如《精通Linux C编程》。

三、编程语言

因为5月份才开始看书，时间很紧张了，考虑到c++比较难，而java相对简单并且做实验室老板的项目一直在用，熟悉感稍微好些(相比c++自从大二之后再也没怎么用过了)，周围的同学都是java系，有问题方便交流些，所以综合考虑后就选择了java系。

1. 首先是java语法基础，这个必须吃透，没商量。当时用的是《java编程思想》，看深入，看仔细。同时要看jdk源码，特别是hashmap那些，面试都要被问到烂了，但还是会问。
2. 当你看深入的时候，会很自然的会追究到底层原理，因此《深入理解java虚拟机》也是要一起吃透的，虚拟机是java系面试的考察重点，重中之重。
3. 基础打好之后，就要往应用方面去扩展，比如并发编程。《java并发编程实战》和《java并发编程的艺术》两本啃下来基本没问题了。
4. 另一个扩展就是恶心的SSM(spring+springMVC+mybatis)那套框架了。不过这个倒不是特别重要，这个没时间看了，只了解个大概，但是貌似不是很影响，遇到的都不会问很深甚至很多没问。感觉大概知道一些关键原理就行，例如AOP、IOC等。
5. 还有就是一些开源的项目，例如阿里的fastjson，还有各种知名的插件，工具，等等。这个属于加分项了，不是必备基础，没有弄。

四、应试重点

提到重点，不得不感谢某中大巨佬，他在github开源了一个复习用的repo：[CyC2018/CS-Notes][1] ，很多人在上面一起总结了很多面试的常问知识点，有针对性地进行复习，可以大大减轻复习压力。

五、关于简历

1. 越简单干净、布局清晰明确，越好
2. 个人介绍最好尽量写核心的技能点，除了说明自己计算机基础扎实以外，可以在面试公司之前先看对应的岗位要求，然后把自己掌握的技能有针对性地写上去。例如面试美团网易的时候就突出熟悉JVM、熟悉Java并发编程、熟悉MySQL和Redis；而面试腾讯百度的时候就突出熟悉Linux进程间编程、熟悉TCP/UDP协议等等
3. 实习经历是非常重要的，特别如果有大厂的实习经历一定要写上去，这个是最最加分的。没有写，因为实验室不让出去实习，很亏很亏
4. 接下来就是项目经验，这个是第二重要的，把做过的项目按技术含量或者成果大小按从高到低顺序写上去，每个项目按“简介、负责、成果”分3点写，尽可能写简练并且突出亮点就行，不用写太详细。但是自己要不断复盘，把项目的所有细节准备清楚，因为面试官感兴趣的话会问得很细，如果自己做的项目都答不上，那么面试官就可能会怀疑这到底是不是你自己的做的项目，大大扣分
5. 面试之前要想做好自己的职业规划，最好结合目前看的书、掌握的技能点来扩展，这样会显得很有计划并且确实在执行，可以让面试官觉得你的职业规划是靠谱的，而不是凭空说大话。例如，跟面试官说目前在看java并发编程和分布式的书，打算以后往分布式架构、微服务这个方向发展，因为个人觉得这个是以后发展的大方向和趋势，然后再扯一些证明的例子，或者支撑理论，这样答下来就会让面试官觉得你确实是认真思考过的
6. 心态放好。不用紧张不用担心，就我面试过的互联网公司来说，大部分一二面的面试官其实也就比我大两三岁，都是很好说话的小哥，态度很好的，就算你一时间答不上来也会慢慢提示，一步步引导你。到了三四面，一般是部门leader，虽然有时候会比较严肃高冷，但是一般不会问很细节的像一二面的基础技术问题，而是一般会考察一些思维上的东西，例如智力题啊，场景题啊之类的开放性题目，只要多看面经总结，找到套路后一般都可以轻松应付（感觉公司都是有自己题库的，每年问来问去都是那么些题，估计也是因为面试官没那么多精力想新题目，所以刷面经是比较有用的）

六、资料汇总

1、算法

1. 《算法(4th)》/黑皮《数据结构与算法分析(Java语言版)》
2. 《算法导论》（学有余力并且想真正提高水平的，可以像的大神舍友一样练习推导和证明）
3. 《剑指Offer》
4. 《程序员代码面试指南》
5. leetcode前200，最好前300题
6. 牛客网的《剑指Offer套题》
7. 《编程珠玑》《编程之美》买了没看，感觉没啥用，层次太高了

2、Java基础

1. 《java编程思想》/《java核心技术(卷1+卷2)》
2. 《深入理解java虚拟机(2th)》
2. 《java并发编程的艺术》
3. 《java并发编程实战》
4. 部分jdk源码（主要是容器类，还有并发包中的锁，CAS，AQS，线程池）
5. Spring+SpringMVC+MyBatis，当时只是看博客跑个小Demo知道怎么用这套框架就算了，原理只是懂个AOP和IOC的大概就没深入看了

3、计算机基础

1. 本科课本《计算机网络》+黑皮《计算机网络：自顶向下方法(6th)》
2. 本科课本《数据库系统概论》+黑皮《数据库系统概念(6th)》+《MySQL技术内幕：InnoDB存储引擎》+《Redis开发与运维》/《Redis设计与实现》
3. 本科课本《操作系统》+黑皮《现代操作系统(4th)》+《精通Linux C编程》

4、应试重点

1. [CyC2018/CS-Notes][2]
2. [jwasham/coding-interview-university][3](这个是进入google的水平，仅供参考，也没看)

5、技能点扩展

1. 《Head First设计模式》
2. 《大型网站技术架构：核心原理与案例分析》(扫盲用)
3. 《轻量级Java EE企业应用实战》(只看了讲IOC和AOP的一小部分)
4. 《深入浅出MyBatis技术原理与实战》(扫盲用)
5. 除了后台基础之外，把掌握的机器学习和Python当作额外技能点写了上去，感觉效果还可以，应该蛮加分的，不少面试官喜欢和我讨论这些内容

[1]: https://github.com/CyC2018/CS-Notes
[2]: https://github.com/CyC2018/CS-Notes
[3]: https://github.com/jwasham/coding-interview-university

