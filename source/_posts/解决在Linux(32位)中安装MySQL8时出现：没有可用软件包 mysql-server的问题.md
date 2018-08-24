---
title: 解决在Linux(32位)中安装MySQL8时出现：没有可用软件包 mysql-server的问题
date: 2018-08-24 14:30:15
categories:
	- Linux
tags: 
	- Linux32位
	- MySQL8
---

首先是前提，做了以下两步：

```
shell> wget http://repo.mysql.com/mysql80-community-release-el7-1.noarch.rpm
shell> rpm -Uvh mysql80-community-release-el7-1.noarch.rpm
```

之后，运行yum -y install mysql-server 时出现“没有可用软件包”的错误。
只要把-server去掉就可以了，如下：

```
shell> yum -y install mysql
```

执行之后会生成MariaDB的yum配置，MariaDB是可以替代MySQL的一个数据库（实际上是MySQL的一个分支），猜测可能MySQL8只做了64位，32位只能用MariaDB替代一下了。
然后执行：

```
shell> yum -y install mariadb-server
```

即可安装MariaDB，然后执行 systemctl start mariadb启动数据库。
启动之后查看进程可以看到，进程名字依然是mysql：
![MariaDB进程](https://upload-images.jianshu.io/upload_images/10709282-4a25331cac4ac3f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
使用方式与MySQL类似，具体参考[博客园星朝的博客](https://www.cnblogs.com/jpfss/p/6568976.html)