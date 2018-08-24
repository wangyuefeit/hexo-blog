---
title: NAT模式，虚拟机可以ping通主机，主机不能ping通虚拟机，主机ping不通虚拟机
date: 2018-08-24 15:28:34
categories: 
	- Linux
tags: 
	- Linux虚拟机
	- CentOS
	- NAT模式
	- ping
---

刚开始设置完静态IP之后，是可以ping通的，之后执行了如下命令：
```
[root@localhost ~]# sudo dhclient
```
然后xshell断了，主机也ping不同虚拟机了。
后来找到问题在于主机的适配器设置中，NAT相关的适配器变成了自动获取IP地址。
右击电脑右下角网络的符号，点击“打开网络和共享中心” -> “更改适配器设置”，就可以看到虚拟机的几个适配器配置，NAT模式的配置一般是VMnet8。
![VMnet8](https://upload-images.jianshu.io/upload_images/10709282-8030590d3011d36f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我的是这样显示的，找了半天……
右键 -> 属性 -> IPv4 -> 属性：
![IPv4](https://upload-images.jianshu.io/upload_images/10709282-bd973a381a51b424.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
设置到和NAT虚拟机一个网段：
![属性](https://upload-images.jianshu.io/upload_images/10709282-74042016a73fdc54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
确定，然后ping虚拟机就通了，xshell也可以连接了。