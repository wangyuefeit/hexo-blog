---
title: Linux CentOS更换国内 阿里/网易 yum源
date: 2018-08-24 14:32:44
categories: 
	- Linux
tags: 
	- Linux
	- CentOS
	- yum源
---


参考阿里云官网[linux下使用yum源和更换国内Yum的方法](https://www.aliyun.com/jiaocheng/130106.html)
####第一步，备份原来的配置文件：

```
[root@localhost~]# cd /etc/yum.repos.d
[root@localhost yum.repos.d]# cp CentOS-Base.repo CentOS-Base.repo.bak
```

####第二步，找到对应版本的repo文件：
阿里镜像地址: http://mirrors.aliyun.com/
网易镜像站:http://mirrors.163.com/
进入网站后找到自己的系统后选择对应的帮助文件，就找到repo文件了，直接用wget命令下载到Linux中。
比如要使用阿里云的 centos-altarch 版本repo，则点击centos-altarch后面的帮助按钮：
![阿里云镜像地址](https://upload-images.jianshu.io/upload_images/10709282-d7d2975c1a2c200d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后直接把命令复制到Linux执行即可。
![帮助](https://upload-images.jianshu.io/upload_images/10709282-ffd290d7d28edde8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
####第三步，清理yum并生成缓存：

```
[root@localhost yum.repos.d]# yum clean all
```

```
[root@localhost yum.repos.d]# yum makecache
```

然后就可以正常使用了，速度要比国外的yum源快很多。