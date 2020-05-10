# Spark完全分布式搭建与使用(4)



十、SPARK WEB UI问题

因为spark有本地模式Local、伪分布式Local-cluster、完全分布式Standalone（集群模式） 三种运行模式。如果不用集群模式去运行spark，在 master:8090 是看不到执行任务的记录的。只能在 master:4040 里面看得到。但是4040只能在任务执行过程中看到，任务执行完后该端口会自动关闭，任务记录被撤销。

spark默认是运行本地模式，例如：$ spark-shell
要运行集群模式，使用：

```
[root@master ~]# spark-shell  --master  spark://master:8070
```

这个端口号要按照8090页面顶头的提示来设置：

![](/2016-06-29-Spark-Spark完全分布式搭建与使用-4/master.jpg)

参考：

- [Spark on YARN两种运行模式介绍][1]
- [Spark运行模式][2]
- [spark 介绍及本地模式、集群模式安装][3]


  [1]: http://www.aboutyun.com/thread-12294-1-1.html
  [2]: http://blog.csdn.net/happyanger6/article/details/47070223
  [3]: http://www.aboutyun.com/thread-8160-1-1.html

