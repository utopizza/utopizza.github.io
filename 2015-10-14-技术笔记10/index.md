# 技术笔记(10)-CentOS7部署



0、准备：centos7镜像文件刻录成U盘系统盘

[Download CentOS][1]

1、服务器是PowerEdge R720，安装系统前要做radis磁盘阵列

[Dell R720上的系统安装问题的解决办法（关于RAID建立磁盘阵列的技术）][2]

2、服务器插上U盘，重启，进入BIOS设置U盘启动，安装centos7(尽量安装英文版否则路径是中文会出现乱码，可以选择安装GNOME桌面版，适用于新手)
   
[centos7.0安装教程][3]

3、配置IP,DNS,GATEWAY,NETMASK,成功之后可以上网

[centos下网络配置方法(网关、dns、ip地址配置)][4]

4、下载并安装LAMP(apache2/httpd，mysql/mariadb，PHP以及PHP相关组件一堆)

[Install Apache, PHP And MySQL On CentOS 7 (LAMP)][5]

5、部署web网站，记得要从防火墙firewall或者iptables开放网站端口，还要留意SELinux这个东西，有可能在阻止你的网站写缓存文件，导致网站故障

[CentOS 7 开放端口][6]
[redhat7 centos7 关闭SELinux和防火墙的办法][7]

6、部署ftp网站，如果想用浏览器访问的话，需要设置允许被动模式，并且开发3000-4000端口，否则只能用ftp命令访问

[CentOS配置VSFTP服务器][8]
[为何客户端软件可以而浏览器则不能连接FTP服务器][9]


  [1]: https://www.centos.org/download/
  [2]: http://www.cnblogs.com/wgp13x/p/3750957.html
  [3]: http://jingyan.baidu.com/article/a3aad71aa180e7b1fa009676.html
  [4]: http://www.jb51.net/os/RedHat/189963.html
  [5]: https://www.howtoforge.com/apache_php_mysql_on_centos_7_lamp
  [6]: http://www.linuxidc.com/Linux/2015-03/114749.htm
  [7]: http://blog.csdn.net/yudar1024/article/details/42341041
  [8]: http://www.cnblogs.com/lightnear/archive/2012/10/07/2713539.html
  [9]: http://www.cnblogs.com/qytan36/archive/2010/05/15/1736270.html

