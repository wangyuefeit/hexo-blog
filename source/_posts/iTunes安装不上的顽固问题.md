---
title: 好累！iTunes安装不上，UNKNOW\Components...，以及注册表“注册表编辑器无法在当前所选的项及其部分子项上设置安全性”，注册表某项打不开、删不掉，所有问题，都在此终结！
date: 2018-08-24 09:48:15
categories:
	- PC使用
tags: 
	- iTunes安装
	- 打不开
	- 删不掉
	- 注册表
---
这个问题困扰了我好几天，每天网上各种搜方法，全都没有用！

本来只是想装一个低版本的iTunes，结果牵出来这么一大坨东西。

第一个问题：安装到一半，程序要写注册表的时候，提示UNKNOW\Components...（这里的...是具体的注册表项），如下图：

![无法打开键](https://upload-images.jianshu.io/upload_images/10709282-cd679125cffde5b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后网上有人抛出解决方法，一共两种，第一种是使用命令提示符的：

![](https://upload-images.jianshu.io/upload_images/10709282-46c48289802a2bde.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

方便复制给出命令行的文本：

secedit /configure /cfg %windir%\inf\defltbase.inf /db defltbase.sdb /verbose

这个是在iTunes吧里看到“lmclmc147”发的。（CSDN编辑器真的很不好用，在这里耽误了好久。。。）

这个方法是可以解决一部分人的问题。

但是有一部分人会出现下列情况：

![](https://upload-images.jianshu.io/upload_images/10709282-7c1441d38df5e9ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样的话，就不会起到任何的作用。

第二种方法是修改注册表权限的：

![](https://upload-images.jianshu.io/upload_images/10709282-447a3832f93bb200.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这是文字描述，换成图片就是：

![](https://upload-images.jianshu.io/upload_images/10709282-d27e43082e028827.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10709282-795698ade2be91eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10709282-2c339e97a05790e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10709282-dba14635c6748b57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10709282-391d4baac7e01ae1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10709282-a724d32ab791acb1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10709282-39de7fd6373dc5e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10709282-9d61443d66550803.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个是在iTunes吧看到“阿绪的画”提供的方法，但实际上执行到这里只执行了一半，下面还需要执行一步：

![](https://upload-images.jianshu.io/upload_images/10709282-9c7e56fe2c4457fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行完这一步之后，就真正获取了该注册表项及所有子项的权限。就可以重新安装iTunes（也有可能是其它软件）了。

但是这一步有可能会出现一个致命的错误，也就是这个错误，困扰了我三天！往下看：

![](https://upload-images.jianshu.io/upload_images/10709282-1d3b5a059cfa4952.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

出现这个错误，就说明上一步执行失败了，那么前面做的所有事情都功亏一篑了。而且这时候你去看UNKNOW\Components...后面的注册表项，你会发现：

![](https://upload-images.jianshu.io/upload_images/10709282-289b65b549f83953.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打不开，删不掉，头大了吧？实际上这个还跟某些C++库有关系，这里就不多说了，先解决问题。

后来在“卡饭论坛”里看到用户“开开心心”发表的：

![](https://upload-images.jianshu.io/upload_images/10709282-dfa677027977b161.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我选择了第二种方法，去微软的官网下载到了这个东西，下载链接：https://docs.microsoft.com/zh-cn/sysinternals/downloads/psexec（请自行复制使用）

下载好之后解压，然后用管理员权限打开命令提示符（win10右击开始，点击带“管理员”的命令提示符），然后定位到解压后的文件夹，关掉打开的注册表，直接执行psexec -i -d -s regedit

有些同学不会定位哈：比如psexec放在D盘tools文件夹下，那么打开命令提示符后，先输入“D:”（不要输入引号，输入D和 冒号）,然后回车，然后输入"cd tools"，进入tools文件夹，看到psexec文件了，这时候执行就可以了。

执行之前不要打开着注册表。执行之后会以system权限启动注册表，然后再执行

![](https://upload-images.jianshu.io/upload_images/10709282-7c82a85d18e92564.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

就不会再提示无法修改，这之后，再去点刚才的注册表项，可以打开了。好，然后重新安装你的iTunes，就会安装成功！

如果还是不行，那么打开注册表HKEY_LOCAL_MACHINE/SOFTWARE/Microsoft/Windows/CurrentVersion/Installer/UserData/S-1-5-18/，然后右击Components，点击权限，选中自己使用的用户，然后勾选上图画红线的部分“使用可从此对象继承的权限项目替换所有子对象 的权限项目(P)”，应用 - 确定，就好了。

PS:

1、卸载软件的时候，尽量使用windows install clean up 卸载的比较干净，也比较强力；

2、有几个C++的运行库，也与上述问题有关，有些答案让卸载C++运行库，尤其是2005的，我也卸载了，不适用WICU（简写了）都删不掉，同样提示注册表权限的。不过就算删掉之后，依然没有注册表权限，依然没什么用。或许能解决部分人的问题吧。