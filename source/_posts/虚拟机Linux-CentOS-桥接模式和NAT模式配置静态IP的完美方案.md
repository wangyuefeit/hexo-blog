---
title: 虚拟机Linux CentOS 桥接模式和NAT模式配置静态IP的完美方案
date: 2018-08-24 15:30:13
categories: 
	- Linux
tags: 
	- Linux虚拟机
	- CentOS
	- 桥接模式
	- NAT模式
	- 静态IP
---

***
配置静态IP实际上就是设置虚拟机的网络配置和linux的3个配置文件
***
### 桥接模式
#####1、首先设置这台linux的网络适配器为桥接模式：
![网络适配器](https://upload-images.jianshu.io/upload_images/10709282-a1fb8fde95cec95a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#####2、查看主机网络配置
Ctrl + R 打开运行，输入cmd回车，然后输入ipconfig回车：
![IP信息](https://upload-images.jianshu.io/upload_images/10709282-0cf0d4bfcb59f369.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
记住IPv4地址和默认网关
#####3、虚拟机打开第一个配置文件：
```
[root@localhost ~]# vim /etc/sysconfig/network-scripts/ifcfg-eth0
```
最后的eth0是网卡名字不是固定的。打开后按照如下修改：
```
DEVICE=eth0
BOOTPROTO=static #静态IP的关键
HWADDR=00:0C:29:17:01:FC
ONBOOT=yes 启动时加载
TYPE=Ethernet
IPADDR=192.168.1.220  #自己设置静态IP，不冲突就可以
NETMASK=255.255.255.0
GATEWAY=192.168.1.1 #在主机配置中看到的默认网关
BROADCAST=192.168.1.255
DNS1=192.168.1.1 #设置和网关一样的值
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
NM_CONTROLLED=yes
```
#####4、修改第二个配置文件：
```
[root@localhost ~]# vim /etc/sysconfig/network
```
按照如下内容进行修改：
```
NETWORKING=yes
NETWORKING_IPV6=no #关掉IPv6
HOSTNAME=localhost.localdomain
GATEWAY=192.168.1.1 #默认网关地址
```
#####5、修改第三个配置文件：
```
[root@localhost ~]# vim /etc/resolv.conf
```
按以下内容修改：
```
search localdomain
nameserver 8.8.8.8 #DNS域名解析地址（首选）
nameserver 192.168.1.1 #（备用）可以写成其它比如114.114.114.114
```
#####6、修改完毕之后重启网络服务：
```
[root@localhost ~]# service network restart
```
***
### NAT模式
#####1、同样，先设置虚拟机的网络适配器为NAT模式：
![网络适配器](https://upload-images.jianshu.io/upload_images/10709282-de0f8c1eb864dcc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#####2、修改VMware的“虚拟网络编辑器”：
点编辑 --> 虚拟网络编辑器：
![编辑](https://upload-images.jianshu.io/upload_images/10709282-1a60a0cc0bf04b7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
打开之后首先点击NAT模式，然后去掉下面DHCP的勾，然后点击NAT设置：
![虚拟网络适配器](https://upload-images.jianshu.io/upload_images/10709282-01407b0fda24d9f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
NAT默认设置了110网段，这个可以修改，修改的话要统一修改不要落下。没有特殊需求就不要修改了。
打开NAT设置面板之后，记住下面红框中这个IP：
![NAT设置](https://upload-images.jianshu.io/upload_images/10709282-6ea5a10840d68cac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#####3、设置完成后，启动虚拟机，修改第一个配置文件，这次要按照NAT设置中的IP进行配置：
```
[root@localhost ~]# vim /etc/sysconfig/network-scripts/ifcfg-eth0
```
按如下改动：
```
DEVICE=eth0
BOOTPROTO=static
HWADDR=00:0C:29:17:01:FC
ONBOOT=yes
TYPE=Ethernet
IPADDR=192.168.110.128 #网段限制在110所以IP地址变了
NETMASK=255.255.255.0
GATEWAY=192.168.110.2 #按照NAT设置中的网关地址设置
BROADCAST=192.168.110.255
DNS1=192.168.110.2
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
NM_CONTROLLED=yes
```
#####4、修改第二个配置文件：
```
[root@localhost ~]# vim /etc/sysconfig/network
```
按如下改动：
```
NETWORKING=yes
NETWORKING_IPV6=no
HOSTNAME=localhost.localdomain
GATEWAY=192.168.110.2
```
#####5、修改第三个配置文件：
```
[root@localhost ~]# vim /etc/resolv.conf
```
按如下改动：
```
search localdomain
nameserver 8.8.8.8
nameserver 192.168.110.2
```
#####6、修改完毕之后重启网络服务：
```
[root@localhost ~]# service network restart
```
***
*这是一个结尾*