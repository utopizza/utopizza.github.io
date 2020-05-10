# Spark完全分布式搭建与使用(1)



一、实验环境

- 宿主机：winserver 2012 服务器一台

- 目标：在三台虚拟机上搭建Spark完全分布式集群，一台master，两台slaves

二、安装虚拟机oracle virtualbox

下载地址：[Download VirtualBox][1]

三、安装 CentOS-6.7-x86_64.iso 镜像文件

1、下载地址：[CentOS Mirror][2]

2、导入到virtualbox中进行安装。（因为三台虚拟机将要进行差不多的软件安装，所以先在一台虚拟机上部署好了master，再将这个虚拟机完全复制两份作为slaves，然后再稍微修改配置文件，这样比较省事）

3、配置：名称为CentOS6_Master，CPU为2个，内存为2G，硬盘等其他参数默认安装。在配置CPU的时候可能无法使用，因为win8 之后的微软操作系统默认安装了Hyper-V虚拟机独占了虚拟资源，可以在：控制面板->程序和功能->启用或关闭Windows功能->启动“删除角色和功能”向导中进行禁用，问题解决。

![](/2016-05-29-Spark-Spark完全分布式搭建与使用-1/Hyper-V-1.jpg)

![](/2016-05-29-Spark-Spark完全分布式搭建与使用-1/Hyper-V-2.jpg)

4、安装好后从“存储”中删除CentOS镜像文件盘片，否则每次启动虚拟机都是进行镜像安装而不是进入虚拟机。另外，安装好后如果宿主机可以上网，那么虚拟机也是可以立即上网的。

![](/2016-05-29-Spark-Spark完全分布式搭建与使用-1/deleteDisk.jpg)

四、安装 java-1.7.0

1、下载安装并验证，参考：[在CentOS上安装Java环境][3]

2、设置JAVA_HOME环境变量（使用 ` ls  –lrt  /etc/alternatives/java ` 来定位jdk的安装路径，参考：[Linux如何查看JDK的安装路径][4]）

```
// 使用vim编辑器进入配置文件
[root@master ~]# vim  /etc/profile

// 修改配置文件
export  JAVA_HOME="/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.101.x86_64" （加入）
export  JRE_HOME=${JAVA_HOME}/jre
export  CLASS_PATH=.:${JAVA_HOME}/lib: ${JRE_HOME}/lib

// 保存并退出编辑器

// 在terminal中输入命令使其生效
[root@master ~]# source  /etc/profile
```

（注意：我这里安装的是openjdk，与常见的sun jdk存放的位置目录不同，参见：[CentOS中JAVA_HOME的环境变量设置][5]）

3、测试是否安装成功

```
[root@master ~]# java -version
```

五、安装 scala-2.10.4

1、因为spark是1.6.1版本，对应scala要用2.10.x版本，否则无法兼容

下载地址：[SCALA 2.10.4][6]

2、解压到系统目录 /usr/lib/ 下

3、配置环境变量

```
[root@master ~]# vim  /etc/profile

export  SCALA_HOME=/usr/lib/scala-2.10.4
export  PATH=$PATH:${SCALA_HOME}/bin

[root@master ~]# source  /etc/profile
```

4、测试

```
[root@master ~]# scala  –version
```

六、安装 SSH

1、查看SSH是否已安装

```
[root@master ~]# rpm –qa | grep ssh
```

2、安装

```
[root@master ~]# yum  install  openssh-server
```

3、启动/关闭/重启

```
[root@master ~]# service  sshd  start/stop/restart
```

4、验证：查看22端口

```
[root@master ~]# netstat  –antp | grep sshd
```

5、设置开机自启/关闭

```
[root@master ~]# chkconfig  sshd  on/off
```

七、复制虚拟机并构建局域网

1、virtualbox虚拟机的网络设置形式有四种（详情参考：[VirtualBox虚拟机网络设置（四种方式）][7]）：

- NAT网络地址转换模式：安装后的默认模式，虚拟机可以访问主机，主机不能访问虚拟机，虚拟机之间不能相互访问。即虚拟机不真实存在于网络中，网络服务由主机单向提供。
- Bridged Adapter网桥模式：虚拟机能被分配到一个网络独立的IP，直接连接入网络中，真是存在于网络。因此虚拟机与主机、虚拟机与虚拟机之间都是可以相互访问的。
- Internal内部网络模式：虚拟机与外部网络完全断开，只实现虚拟机之间的内部网络模式。虚拟无法访问主机，也不能通过主机连接外部网络。
- Host-only Adapter完全主机模式：最复杂，虚拟出一张网卡供虚拟机使用，灵活性最高。

2、复制虚拟机，选择完全复制即可，修改名字为 CentOS6_Worker1 、 CentOS6_Worker2 

3、配置局域网，使三台虚拟机能相互ping通，并且能SSH免密码登录（因为本来想设置最好的Bridged Adapter网桥模式，但是尝试了几次都失败了，最后采用比较简单的Internal内部网络模式。如果你也选择该模式，请提前下载好Hadoop和Spark的安装包，因为部署好内部网络后将与外部网络断开无法上网下载）：

(1)、先在宿主机（winServer）的cmd中启动vituralbox的dhcp服务，设置一个vituralbox内部网络（参考：[Virtualbox下使用internal networking做一个小局域网][8]）：

```
>C:\Program Files\Oracle\VirtualBox>VBoxManage.exe dhcpserver add
--netname  intnet 
--ip  192.168.1.1 
--netmask  255.255.255.0 
--lowerip  192.168.1.1 
--upperip  192.168.1.100 
--enable
```

(2)、修改虚拟机->设置->网络

![](/2016-05-29-Spark-Spark完全分布式搭建与使用-1/intnet.jpg)

(3)、启动三台虚拟机，分别添加新的网络连接配置“eth0”。其中，掩码都是255.255.255.0，网关都是192.168.1.1，各自IP如下：

- Master：192.168.1.2
- Worker1：192.168.1.3
- Worker2：192.168.1.4

![](/2016-05-29-Spark-Spark完全分布式搭建与使用-1/IP-1.jpg)

设置完后，重启eth0端口

```
[root@master ~]# ifdown  eth0
[root@master ~]# ifup  eth0
```

查看网络设置

```
[root@master ~]# ifconfig
```

如果下图所示就说明设置好了。（安装的无桌面版本CentOS的高手请忽略）

![](/2016-05-29-Spark-Spark完全分布式搭建与使用-1/IP-2.jpg)

(4)、关闭三台虚拟机的防火墙，然后用ping分别进行测试（参考：[centOS 6.5关闭防火墙步骤][9]）

(5)、设置每台虚拟机的网络主机名，这步只是为了方便配置，不用每次都写一堆那么长的IP地址。要先获得root权限才能进行如下操作（下面以master为例）：

step 1：

```
[root@master ~]# vim  /etc/hosts

// 进入该配置文件后，清掉原来的默认设置，然后加入：
127.0.0.1       localhost
192.168.1.2     Master
192.168.1.3     Worker1
192.168.1.4     Worker2
```

注意：这个配置文件很关键，**一定要把原来默认的地址全部去掉，否则之后安装hadoop的网页UI会无法正常工作**。参见：[Hadoop在master查看live nodes为0解决方案][10]

step 2：

```
[root@master ~]# vim  /etc/sysconfig/network
//将里面的HOSTNAME改为master
```

step 3：再使用hostname命令指定一次

```
[root@master ~]# hostname  master
```

step 4：
重新登录/重启一次，这时如果配置成功，就会看见命令提示字符串变成"[root@master ~]#"

(6)、以上，三台机器都设置好各自网络主机名后，再用ping测试

![](/2016-05-29-Spark-Spark完全分布式搭建与使用-1/IP-3.jpg)

(7)、设置master到两个worker虚拟机SSH免密码登录

step 1：在master上面进入root的.ssh目录

```
[root@master ~]# cd  ~/.ssh
//如果没有该目录，就用mkdir  ~/.ssh建立并进入
```

step 2：使用 ` ssh-keygen  –t  rsa ` 来生成公钥和私钥，连续回车，不设置密码

step 3：把公钥文件复制到要其他机器的root用户目录下的.ssh目录（如果不是root用户，需要在前面加/home/）

```
[root@master ~]# scp  ~/.ssh/id_rsa.pub  root@master:/root/.ssh/authorized_keys
[root@master ~]# scp  ~/.ssh/id_rsa.pub  root@worker1:/root/.ssh/authorized_keys
[root@master ~]# scp  ~/.ssh/id_rsa.pub  root@worker2:/root/.ssh/authorized_keys
```

step 4：检测master是否可以不需要密码登录到worker1和worker2

```
[root@master ~]# ssh  master
[root@master ~]# ssh  root@worker1
[root@master ~]# ssh  root@worker2
```

至此，所有准备步骤基本完成，下一步可以开始正式安装hadoop、spark






  [1]: https://www.virtualbox.org/wiki/Downloads
  [2]: http://isoredirect.centos.org/centos/6/isos/x86_64/
  [3]: http://jingyan.baidu.com/article/4853e1e51d0c101909f72607.html
  [4]: http://www.cnblogs.com/kerrycode/archive/2015/08/27/4762921.html
  [5]: http://blog.csdn.net/wind520/article/details/9308809
  [6]: http://www.scala-lang.org/download/2.10.4.html
  [7]: https://www.douban.com/group/topic/15558388/
  [8]: http://blog.csdn.net/fyifei0558/article/details/45506271
  [9]: http://blog.163.com/cdma2368@126/blog/static/301742762014113103036962/
  [10]: http://blog.csdn.net/shenlan211314/article/details/7414728

