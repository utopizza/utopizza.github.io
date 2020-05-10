# Spark完全分布式搭建与使用(2)



八、安装Hadoop-2.7.2

- 说明：如果是想用 “Spark on Standalon” 模式就不用安装Hadoop，如果想用 “Spark on Yarn” 或者需要去hdfs取数据则应先装Hadoop
- 参见：
    - [CentOS7 上安装Hadoop 2.7.2 的安装 和 初步使用][1]）
    - [Hadoop2.6, Red hat Linux 6.6 x64集群完全分布式安装][2]
    - [CentOS7安装Hadoop2.7完整流程][3]

1、下载地址：[Apache Hadoop Releases Download][4]

这里我选择binary文件进行下载，这种是已经编译好的源码，下载的文件名是hadoop-2.6.4.tar.gz；如果喜欢自己编译，可以选择source文件进行下载，下载的文件名是hadoop-2.7.2-src.tar.gz。（参考：[hadoop两个安装包分别是干啥的？][5]）

![](/2016-06-10-Spark-Spark完全分布式搭建与使用-2/download.jpg)

2、将安装包移动到到路径：` /usr/local/ `，并解压

```
[root@master ~]# cd  /usr/local
[root@master ~]# tar  -xzvf  hadoop-2.7.2.tar.gz
```
	
3、判断Hadoop版本

```
[root@master ~]# /usr/local/hadoop-2.7.2/bin/hadoop  version
```

4、配置环境变量

```
[root@master ~]# vim  /etc/profile

export  HADOOP_HOME=/usr/local/hadoop-2.7.2
export  PATH=$PATH:{HADOOP_HOME}/bin:{HADOOP_HOME}/sbin

[root@master ~]# source  /etc/profile
```

同步到worker1和worker2

```
[root@master ~]# scp  /etc/profile  root@worker1:/etc/profile
[root@master ~]# ssh  root@worker1
[root@worker1 ~]# source  /etc/profile
[root@worker1 ~]# exit

[root@master ~]# scp  /etc/profile  root@worker2:/etc/profile
[root@master ~]# ssh  root@worker2
[root@worker2 ~]# source  /etc/profile
[root@worker2 ~]# exit
```

5、配置Hadoop文件

step 1：在master本地创建以下文件夹

- /home/Hadoop/name
- /home/hadoop/data
- /home/hadoop/temp

step 2：进入目录 ` /usr/local/hadoop-2.7.2/etc/hadoop/ ` ，修改配置文件，共7个

(1) hadoop-env.sh：修改 `JAVA_HOME` 值  

```
export  JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.101.x86_64
```

(2) yarn-env.sh：修改 `JAVA_HOME` 值  

```
export  JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.101.x86_64
```

(3) slaves：写入 

```
worker1
worker2
```

(4) core-site.xml

```
<configuration>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://master:9000</value>
        </property>
        <property>
                <name>hadoop.tmp.dir</name>
                <value>file:/home/hadoop/temp</value>
                <description>Abase for other temporary directories.</description>
        </property>
</configuration>
```

(5) hdfs-site.xml

```
<configuration>
         <property>
               <name>dfs.namenode.secondary.http-address</name>
               <value>master:9001</value>
         </property>
         <property>
                 <name>dfs.namenode.name.dir</name>
                 <value>file:/home/hadoop/name</value>
         </property>
         <property>
                 <name>dfs.datanode.data.dir</name>
                 <value>file:/home/hadoop/data</value>
         </property>
         <property>
                 <name>dfs.replication</name>
                 <value>2</value>
         </property>
         <property>
                 <name>dfs.webhdfs.enabled</name>
                 <value>true</value>
         </property>
</configuration>
```

(6) mapred-site.xml

```
<configuration>
                <property>
                         <name>mapreduce.framework.name</name>
                         <value>yarn</value>
                </property>
                <property>
                         <name>mapreduce.jobhistory.address</name>
                         <value>master:10020</value>
                </property>
                <property>
                         <name>mapreduce.jobhistory.webapp.address</name>
                         <value> master:19888</value>
                </property>
</configuration>
```

(7) yarn-site.xml

```
<configuration>
        <property>
               <name>yarn.nodemanager.aux-services</name>
               <value>mapreduce_shuffle</value>
        </property>
        <property>
               <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
               <value>org.apache.hadoop.mapred.ShuffleHandler</value>
        </property>
        <property>
               <name>yarn.resourcemanager.address</name>
               <value> master:8032</value>
       </property>
       <property>
               <name>yarn.resourcemanager.scheduler.address</name>
               <value>master:8030</value>
       </property>
       <property>
               <name>yarn.resourcemanager.resource-tracker.address</name>
               <value>master:8031</value>
       </property>
       <property>
               <name>yarn.resourcemanager.admin.address</name>
               <value> master:8033</value>
       </property>
       <property>
               <name>yarn.resourcemanager.webapp.address</name>
               <value> master:8088</value>
       </property>
</configuration>
```

step 3：复制整个hadoop目录到worker1和worker2

```
[root@master ~]# scp  –r  /usr/local/hadoop-2.7.2  root@worker1:/usr/local/hadoop-2.7.2
[root@master ~]# scp  –r  /usr/local/hadoop-2.7.2  root@worker2:/usr/local/hadoop-2.7.2
```

6、启动 hadoop

step 1：进入安装目录

```
[root@master ~]# cd  /usr/local/hadoop-2.7.2
```

step 2：格式化 namenode，成功的话会看到“successfully formatted”和“exitting with status 0”

```
[root@master ~]# ./bin/hdfs  namenode  -format
```

注意，**以后重新格式化可能导致datanode无法启动，需要手动更新集群ID**，参考：

- [重新格式化HDFS的方法][6]
- [Hadoop中重新格式化namenode][7]
	
step 3：启动 hdfs

```
[root@master ~]# ./sbin/start-dfs.sh
```

step 4：启动 yarn

```
[root@master ~]# ./sbin/start-yarn.sh
```
			
step 5：用 `jps` 命令来查看是否启动成功，若成功会看到master上面有

- namenode
- secondarynamenode
- resourcemanager

在worker1和worker2上面有

- datanode
- nodemanager

step 6：查看集群状态

```
[root@master ~]# ./sbin/hdfs  dfsadmin  -report
```

或者在浏览器打开：`http://master:50070` 和 `http://master:8088` 查看。注意 `live nodes` 的个数

![](/2016-06-10-Spark-Spark完全分布式搭建与使用-2/hadoop-1.jpg)

![](/2016-06-10-Spark-Spark完全分布式搭建与使用-2/hadoop-2.jpg)

7、HadoopH的其他操作，略。参考：[Hadoop的启动与停止][8]





  [1]: http://blog.csdn.net/remote_roamer/article/details/50579874
  [2]: http://www.dataguru.cn/thread-530951-1-1.html
  [3]: http://www.open-open.com/lib/view/open1435761287778.html
  [4]: http://hadoop.apache.org/releases.html
  [5]: http://www.itpub.net/thread-1875856-1-1.html
  [6]: http://blog.csdn.net/yeruby/article/details/21542465
  [7]: https://my.oschina.net/HIJAY/blog/220816
  [8]: http://book.2cto.com/201401/39823.html

