# Spark完全分布式搭建与使用(5)



十一、搭建Spark程序开发环境IDEA

1、下载地址：[IDEA Download][1]
2、解压并移动到路径：`/usr/local/idea-IC-145.597.3`
3、安装，进入 `/usr/local/idea-IC-145.597.3/bin`，运行

```
[root@master ~]# idea.sh
```

4、在安装导向过程中，选择下载并安装SCALA插件。参考：[Spark入门实战系列--3.Spark编程模型（下）--IDEA搭建及实战][2]
 
5、安装完成后，新建SCALA工程：`File->New Project->Scala->Scala->Next`，然后填入Project的名字，选择项目目录、SDK、Scala SDK，一般默认即可。然选择Finish。

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-5/idea-1.jpg)

6、在src文件夹下直接新建一个scala类对象：右键 `src->new->scala class`，命名，并填入代码。（这里要注意，在src文件下不要再建立嵌套的source类型的文件（像src这样的蓝色的文件夹），否则无法运行。关于这一点，上面的参考帖子里面是错的，也可能他那个是旧版本才支持的，这个问题具体我没有深入研究，我取消了中间嵌套的所有source文件夹就可以正常运行了）

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-5/idea-2.jpg)

7、设置library，导入jar包。添加两个即可，一个是Scala SDK Library，这里选择scala-2.10.4版本。另一个是`$SPARK_HOME/lib/spark-assembly-1.1.0-hadoop2.2.0.jar`

(1) 进入设置界面

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-5/library-1.jpg)

(2) 添加JAVA包

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-5/library-2.jpg)

(3) 找到本地的Spark的安装目录下的lib，选择assembly包

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-5/library-3.jpg)

(4) 添加Scala SDK

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-5/library-4.jpg)

(5) 选择本地安装的scala-2.10.4

![](/2016-07-05-Spark-Spark完全分布式搭建与使用-5/library-5.jpg)



  [1]: https://www.jetbrains.com/idea/#chooseYourEdition
  [2]: http://www.cnblogs.com/shishanyuan/p/4721120.html

