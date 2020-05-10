# Spark完全分布式搭建与使用(3)



九、安装Spark-1.6.1-bin-hadoop2.6

1、下载：[Download Apache Spark][1]

![](/2016-06-22-Spark-Spark完全分布式搭建与使用-3/download.jpg)

2、将安装包移动到到路径：`/usr/local/`，并解压

```
[root@master ~]# cd  /usr/local/
[root@master ~]# tar  -xzvf  spark-1.6.1-bin-hadoop2.6.tar.gz
```

3、配置环境变量

```
[root@master ~]# vim  /etc/profile

export  SPARK_HOME=/usr/local/spark-1.6.1-bin-hadoop2.6
export  PATH=${SPARK_HOME}/bin: ${SPARK_HOME}/sbin
```

现在可以将所有的PATH整合到一起：

```
export  JAVA_HOME="/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.101.x86_64"
export  JRE_HOME=${JAVA_HOME}/jre
export  CLASS_PATH=.:${JAVA_HOME}/lib: ${JRE_HOME}/lib
export  SCALA_HOME=/usr/lib/scala-2.10.4
export  HADOOP_HOME=/usr/local/hadoop-2.7.2
export  SPARK_HOME=/usr/local/spark-1.6.1-bin-hadoop2.6
export  PATH=$PATH:${JAVA_HOME}/bin:${SCALA_HOME}/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:${SPARK_HOME}/bin: ${SPARK_HOME}/sbin
```

![](/2016-06-22-Spark-Spark完全分布式搭建与使用-3/path.jpg)

配置完成后

```
[root@master ~]# source  /etc/profile
```

同步到worker1和worker2

```
[root@master ~]# scp  /etc/profile  root@worker1:/etc/profile
[root@master ~]# ssh  root@worker1
[root@worker1 ~]# source  /etc/profile
[root@worker1 ~]# exit

[root@master ~]# scp  /etc/profile  root@worker2:/etc/profile
[root@master ~]# ssh  root@worker1
[root@worker2 ~]# source  /etc/profile
[root@worker2 ~]# exit
```

4、配置Spark文件

```
[root@master ~]# cd  /usr/local/spark-1.6.1-bin-hadoop2.6/conf
```

step 1：spark-env.sh

```
[root@master ~]# cp  spark-env.sh.template  spark-env.sh
[root@master ~]# vim  spark-env.sh

export  JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.101.x86_64
export  SCALA_HOME=/usr/lib/scala-2.10.4 
export  SPARK_HOME=/usr/local/spark-1.6.1-bin-hadoop2.6
export  SPARK_MASTER_IP=master
export  SPARK_MASTER_PORT=8070
export  SPARK_MASTER_WEBUI_PORT=8091
export  SPARK_WORKER_INSTANCES=2
export  SPARK_WORKER_MEMORY=1g
export  HADOOP_CONF_DIR=/usr/local/hadoop-2.7.2/etc/hadoop
```

step2：slaves.template

```
[root@master ~]# cp  slaves.template  slaves
[root@master ~]# vim  slaves

// 加入
worker1
worker2
```

step 3：复制spark到worker1和worker2

```
[root@master ~]# scp  –r  /usr/local/spark-1.6.1-bin-hadoop2.6  root@worker1:/usr/local/spark-1.6.1-bin-hadoop2.6
[root@master ~]# scp  –r  /usr/local/spark-1.6.1-bin-hadoop2.6  root@worker2:/usr/local/spark-1.6.1-bin-hadoop2.6
```

5、启动Spark集群

```
[root@master ~]# /usr/local/spark-1.6.1-bin-hadoop2.6/sbin/start-all.sh
```

用命令 `jps` 查看，如果有master和worker进程说明启动成功。可以通过 `http://master:8091` 查看集群情况和运行的applications。也可以在 `http://master:4040` 查看application运行的job的具体信息

6、用例测试，略。

7、本文参考：

- [Spark安装启动 and 在程序中调用spark服务][2]
- [Spark 伪分布式 & 全分布式 安装指南][3]
- [Spark1.6.0 on Hadoop-2.6.3 安装配置][4]
- [Spark1.4.1完全分布集群hadoop2.6.0上部署][5] 


  [1]: http://spark.apache.org/downloads.html
  [2]: https://my.oschina.net/132722/blog/196027?fromerr=UObz2ulL
  [3]: http://www.open-open.com/lib/view/open1427935743396.html
  [4]: http://www.thebigdata.cn/Hadoop/28957.html
  [5]: http://www.dataguru.cn/thread-530953-1-1.html

