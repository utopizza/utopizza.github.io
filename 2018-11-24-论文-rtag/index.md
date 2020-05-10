# RTAG


一、背景及问题

目前出现一些高级的多主机攻击手段，它们通过使用“command-and-control(C&C)”通道或者代理服务器来实现攻击者隐藏和攻击方式隐藏。例如 [GitPwnd][1] 攻击，它利用了 Git 的同步机制来泄漏被攻击者的私有数据，大致过程如下：

 1. 攻击者事先在一个流行的 git repository 的镜像中 放置一个钩子脚本，这个脚本会指向一个恶意的 C&C repository
 2. 当被攻击者用 git 复制该 repository 镜像时，git 会自动地连带那个恶意 C&C repository 一起复制到被攻击者的 git 服务器
 3. 当被攻击者在 repository 上执行某些特定操作如 git commit 时，恶意 C&C repository 被触发，把被攻击者的数据私下发送到攻击者的主机而不会被察觉

针对这类攻击，一种称为动态信息流追踪（Dynamic information flow tracking , DIFT）的技术被提出并应用于攻击侦查。然而，目前存在的大多数攻击侦查系统只能对单个主机的攻击进行侦查，例如对某个主机下的 system-call-level 或 instruction-level 的事件进行监控，而无法侦查如上述 GitPwnd 之类的跨主机攻击。

二、解决方案

论文提出了一种基于记录-回放的信息流标签和追踪系统（a record-and-replay-based data flow tagging and tracking system, called RTAG）。RTAG 首先通过隔断标签依赖关系（主机之间的信息流）来摆脱多个主机之间信息流顺序的约束，使 DIFT 可以并行执行。其次，RTAG 通过精心设计的数据结构来减少 DIFT 的内存消耗（90%）和时间消耗（60%-90%）。 

标签系统 RTAG 的大致设计思路如下：

1. 使用 <主机 mac 地址 + inode + dev + crtime + byte-offset> 来实现全局唯一标签（global tag），标识感兴趣文件中的每个字节，实现对文件在多个主机之间的信息流的追踪
2. 使用与 RAIN 系统一样的资源图（provenance graph） 来追踪程序之间的信息流，如程序到文件、程序到程序、文件到程序
3. 使用可达性分析算法，对多主机的全局资源图进行剪枝，得到只与攻击相关的资源子图，减少后续分析的计算量。为实现此跨主机的可达性追踪，RAIN 需要在 socket 层次（TCP & UDP）上对主机之间的网络通讯进行监控
5. 采用与 RAIN 系统一样的记录-回放策略（record-and-replay），即平时只记录信息流动的信息，只有在执行攻击分析查询时再通过回放记录来执行 DIFT（因为计算量和内存消耗较大，无法实时在线执行）
6. 使用 32-bit 的数据版本号来隔断 tags 之间的依赖关系，使得每个 replay 可以单独执行而无需执行所依赖的数据版本的 replay。数据版本号在数据（即文件的数据）被执行 write() 之类的系统调用时自动增加
7. 使用 global tag 和 local tag 转换技术来减低资源消耗量。因为在单个主机内执行攻击分析时，没有必要因为使用 global tag 而耗费内存资源。因此在执行这种单个主机内的 DIFT 之前，RTAG 会先把 global tag 转换成 local tag，待执行完后再换回来

三、总结

总的来说，文章提出的 RTAG 系统是在原有的 RAIN 系统上，对其标签系统（tagging system）进行扩展而得到的。它通过使用全局标签来进行跨主机的信息流追踪，使用全局-局部标签转换来降低计算量等等，使得原有的单主机 DIFT 系统 RAIN 扩展到了多主机的攻击侦查。


 
  [1]: https://github.com/nccgroup/gitpwnd

