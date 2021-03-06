# FDSD


一、背景及问题

目前一些高级的网络攻击例如 APT (Advanced Persistent Threat) 引起了工业界和学术界的广泛关注和研究。这种攻击的特点是隐性、长期、多步骤。当一次攻击被发现后，鉴定分析 (Forensic Analysis) 系统就会开始被执行以确定攻击的入口以及攻击的影响范围。鉴定分析是基于系统日志进行的，它通过分析日志文件（Log）中记录的所有系统操作（例如网络事件、文件读写事件、进程间通信事件）之间的信息流，来追踪攻击源头，以及被攻击所污染的所有实体。由此可知，如果要使的分析结果越精确，那么日志文件则需要越详细地记录系统发生的一切事件，如此一来日志文件就不得不变得更大。再考虑到 APT 的一次攻击可能持续几个月的时间，以及大型企业一般都会有成千上万台主机（Host），在这种情况下日志文件可以轻松达到 PB 级别，不但需要占用巨大的存储空间，而且还会拖慢鉴定分析的速度。

此问题吸引了众多研究团体的兴趣，他们主要关注于如何在保证不影响鉴定分析的精确率的前提下，减小日志体积，提高鉴定分析的效率。

二、解决方案

文章提出了一种基于依赖图（Dependence Graph）的日志压缩技术，在不影响鉴定分析准确性的前提下，可以大幅度减少日志记录。大体思路如下：

 1. 系统日志记录的系统事件可以用依赖图表示。系统内的实体（如进程、文件、网络等）用结点表示，系统事件（如系统调用，读写文件、进程间消息通讯、网络数据传输等）用带时间戳的有向边表示，其指向方向与信息/数据流动方向一致
 2. 只针对 read、write、load 三类系统事件进行压缩，其他系统事件如 fork、execve、remove、rename、chmod 等等不考虑压缩直接保留。因为根据经验统计，系统中超过 95% 的事件是读写事件，只要这部分的日志减下来即可。并且只对读写事件进行压缩可以降低算法模型的复杂度，提高可读性
 3. 根据前向可达性（Forward Reachability）和后向可达性（Backward Reachability），定义两种子图保留方案：全依赖 (Full Dependence FD) 保留，资源依赖（Source Dependence，SD）保留
 4. 由于依赖图的边（即系统事件）是带时间戳的，当执行可达性计算时，需要对一个节点按时间戳的先后顺序多次计算，而不能像标准的图算法那样对所有结点一视同仁地一次性计算并进行中间结果缓存。这样显然会提高算法的复杂度，因此文章提出了一个算法，将带时间戳的依赖图转化标准图（边不带时间戳），具体方法是对结点进行多版本化（Versioning）
 5. 但是如果只简单地进行结点的多版本化，又会增大图的复杂度，增大日志的体积，因此在多版本化后，文章提出了几种剪枝优化算法来简化依赖图：Redundant edge optimization (REO)、Global Redundant Edge Optimization (REO*)、Redundant node optimization (RNO)、 Redundant node optimization (RNO)
 6. 通过以上步骤对依赖图进行简化之后，就可以将依赖图还原成日志文件，此时的日志文件中只保留了关键的信息，体积大大减小。但是文章认为日志文件原来的格式不够节省空间，于是提出了一种新的压缩文件格式 CSR ，可以进一步减少空间占用

三、总结

文章主要针对鉴定分析需要的日志文件过大问题，提出了一种在保证不影响侦查分析准确性的前提下，优化依赖子图、减少日志文件体积、提高鉴定分析效率的方案，其主要思路是通过前后可达性和后向可达性来保证依赖子图保留了关键的结点和边。

