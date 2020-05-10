# Spark完全分布式搭建与使用(6)



十二、运行Spark分布式程序

按照前面博客设置好Library后，选择Modules，可以看到导入的包列表如图（注意，这里包的顺序是会对项目的编译造成影响的，好像要把spark的包放在scala的包之前，否则编译的时候会报一些找不到某些函数的错误，具体问题忘了记录了，这个可以自己慢慢试试，百度上也有解决方案）

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-6/modules.jpg)

到这里，就可以运行单机/本地模式的spark程序了。要跑本地程序，命令如下（scala）：

```
val conf=new SparkConf().setAppName(“XXXX”).setMaster(“local”)
```

但是如果要运行集群模式的spark程序，必须还要将程序项目打包，并将这个程序分发到集群各个节点运行。要使程序分布式运行，命令如下：

```
val conf=new SparkConf().setAppName(“XXXX”).setMaster(“spark://master:8070”).setJars(List(“XXX”))
```

其中的setMaster()里面的主机名和端口号需要根据你自己的实际配置进行设定，见第十节。而setJars()是本项目经过编译打包后的jar包的路径，要运行分布式程序必须添加这个包，否则会报错说workers找不到各种类

项目打包步骤如下：

(1) 进入 Artifacts->JAR->From modules with …

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-6/package-1.jpg)

(2) 填入所建立的scala class的名字

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-6/package-2.jpg)

(3) 选择Build on make，并记下项目打包输出的位置（重要！！）

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-6/package-3.jpg)

设置完后，编译本项目。第一次编译的时候先不要在源码使用.setJars()这个函数。让编译通过。编译通过后，打开控制台，通过刚才的目录找到生成的Jar包。修改源码，加入这个包。在这里可以删除 “output root” 下的scala和spark的包，因为这两个包的导出没有什么意义。

(4) 根据路径找到与项目同名的JAR包：

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-6/package-4.jpg)

(5) 运行Spark的分布式程序

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-6/package-5.jpg)

(6) 正确运行结果

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-6/package-6.jpg)

注意，如果程序运行一半，没有打印结果直接结束，有可能是内存不足导致程序直接死亡。这种情况下，可能不会报任何错误，在 WEB UI 上也只是显示 Finished 。我当时给master分配的内存从2G扩展到4G，才解决了这个问题，正确运行出结果。

参考：

- [使用IDEA开发SPARK提交remote cluster执行][1]
- [Windows下IntelliJ IDEA中调试Spark Standalone][2]
- [Spark入门实战系列--3.Spark编程模型（下）--IDEA搭建及实战][3] 

十三、使用Spark的机器学习库MLlib

我运行了其中的k-means算法，比较简单这里不再赘述，可参考：

- [使用 Spark MLlib 做 K-means 聚类分析][4]
- [Scala Standard Library][5]
- [Spark学习1： 基础函数功能解读][6]


  [1]: http://www.cnblogs.com/gaoxing/p/4414362.html
  [2]: http://blog.csdn.net/javastart/article/details/43372977
  [3]: http://www.cnblogs.com/shishanyuan/p/4721120.html
  [4]: https://www.ibm.com/developerworks/cn/opensource/os-cn-spark-practice4/
  [5]: http://www.scala-lang.org/api/current/#scala.collection.immutable.StringLike
  [6]: http://blog.csdn.net/yunlong34574/article/details/38635853

