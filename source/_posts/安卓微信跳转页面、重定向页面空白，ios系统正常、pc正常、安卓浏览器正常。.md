---
title: 安卓微信跳转页面、重定向页面空白，ios系统正常、pc正常、安卓浏览器正常。
categories:
  - Java
tags:
  - Java
  - 微信
  - 安卓
date: 2018-08-24 15:53:09
---
这个问题是重定向页面地址参数的问题，参数中有“.”。

由于调用微信支付某个接口要传client_ip，所以在调用服务器接口时，先获取到调用端IP地址，然后写在重定向地址中，等待微信重定向的时候带上这个参数。结果就出现了安卓微信出现空白页面。查看后台日志发现没有跳转到重定向的地址。

再后来判断是参数的问题，于是将嫌疑最大的client_ip去掉，就可以正常跳转了。

解决方法有二：
1. 一个是用其它符号比如“_”，代替“.”，到重定向完毕之后，再替换回“.”，就可以了；

2. 二是直接去掉这个参数，再想办法在重定向之后获取。