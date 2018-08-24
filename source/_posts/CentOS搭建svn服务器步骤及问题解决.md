---
title: CentOS搭建svn服务器步骤及问题解决
date: 2018-08-24 14:27:15
categories: 
	- Linux
tags: 
	- CentOS
	- svn服务器
---

###第一步，安装SVN：

```
shell> yum -y install subversion
```

###第二步，创建版本库：
例如在home下创建版本库：

```
shell> cd /home
shell> mkdir svn
shell> mkdir svn/test
shell> svnadmin create /home/svn/test
```

这样就创建好了一个版本库，会在test路径下生成几个默认的目录。
###第三步，配置版本库：
进入test目录的conf目录，有3个配置文件：
首先打开svnserve.conf进行配置，添加如下配置即可，或者修改原有配置：

```
anon-access = read
auth-access = write
password-db = passwd  #该配置是定位 配置密码的文件
authz-db = authz  #改配置是定位 配置权限的文件
realm = My First Repository
```

然后打开passwd文件进行配置，在[users]下面添加用户名和密码，可以添加多个，例如：

```
bruce = 123456
mike = 111111
```

最后对权限进行配置，打开authz文件：

```
[groups]  #对用户进行分组
admin = bruce
users = mike
[/]  #这样写相当于对svn的根目录赋予权限，刚开始可以先按这样配置
@admin = rw  #加@符表名后面是一个分组名，不加则是用户名
* = r #*表示所有人，r表示读权限，w表示写权限
```

###第四步，启动服务：
启动服务不需要使用系统的服务启动命令，会报一些不必要的错误，直接使用如下命令即可：

```
shell> svnserve -d -r /home/svn
```

如果路径写到test，连接svn路径就按：svn://192.168.110.20，建议启动路径写到svn，连接时如此写：svn://192.168.110.20/test。
默认监听3690端口，想自定义监听端口可以在命令后面增加：

```
--listen-port 端口号
```