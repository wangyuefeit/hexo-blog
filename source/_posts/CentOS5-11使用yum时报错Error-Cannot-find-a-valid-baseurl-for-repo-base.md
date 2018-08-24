---
title: 'CentOS5.11使用yum时报错Error: Cannot find a valid baseurl for repo: base'
date: 2018-08-24 14:34:31
categories: 
	- Linux
tags:
	- Linux
	- CentOS
	- yum
---

这个问题是前几天发现的，在电脑上安装虚拟机，然后在上面装了大学时老师给的linux景象。配置好静态地址之后，使用yum安装jdk，发现报了下面的错误：

```
[root@localhost ~]# yum install java-1.8.0-openjdk*
Loaded plugins: fastestmirror, security
Loading mirror speeds from cached hostfile
YumRepo Error: All mirror URLs are not using ftp, http[s] or file.
 Eg. Invalid release/
removing mirrorlist with no valid mirrors: /var/cache/yum/base/mirrorlist.txt
Error: Cannot find a valid baseurl for repo: base
```

在网上查遍了各种办法都不奏效，后来把配置文件（/etc/yum.repos.d/CentOS-Base.repo）中的url拷贝到浏览器访问了一下，真的是url无效导致的。
于是直接访问了mirror.centos.org，点击到5.11之后，打开readme，显示如下：

```
This directory (and version of CentOS) is depreciated.  
CentOS-5 is now past EOL
You can get the last released version of centos 5.11 here:
http://vault.centos.org/5.11/
Please NOTE:  this is not being maintained for security since moving to Vault.
It will have security issues, you should upgrade to a new version instead.
```

于是把配置文件中所有mirrorlist注释掉，把baseurl都改成：

```
http://vault.centos.org/5.11/centosplus/$basearch/
```

然后就可以了。
最后贴一份完整的配置文件，修改之前记得备份：

```
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-5.11 - Base
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=os
baseurl=http://vault.centos.org/5.11/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#released updates
[updates]
name=CentOS-5.11 - Updates
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=updates
baseurl=http://vault.centos.org/5.11/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#packages used/produced in the build but not released
[addons]
name=CentOS-5.11 - Addons
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=addons
baseurl=http://vault.centos.org/5.11/addons/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#additional packages that may be useful
[extras]
name=CentOS-5.11 - Extras
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=extras
baseurl=http://vault.centos.org/5.11/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-5.11 - Plus
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=centosplus
baseurl=http://vault.centos.org/5.11/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#contrib - packages by Centos Users
[contrib]
name=CentOS-5.11 - Contrib
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=contrib
baseurl=http://vault.centos.org/5.11/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
```
