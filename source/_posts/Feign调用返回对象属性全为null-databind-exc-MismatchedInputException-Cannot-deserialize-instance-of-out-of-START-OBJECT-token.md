---
title: >-
  Feign调用返回对象属性全为null | databind.exc.MismatchedInputException: Cannot
  deserialize instance of '' out of START_OBJECT token
categories:
  - Java
tags:
  - Java
  - SpringCloud
  - Feign
date: 2018-08-24 15:47:54
---
Feign是SpringCloud中的一个模块，作用是将本来需要发送请求的工作简化成为调用方法就可以完成的简单工作。
#####最近在使用Feign的时候，经常出现两种情况：
1. 请求返回一个对象，请求成功了，没有熔断，也返回数据了，而且对象不为null，但是对象所有的属性都是null，这样直接通过了我们的验空判断，从而造成报错。
2. 返回一个对象List时，报错如下：
```
databind.exc.MismatchedInputException: Cannot deserialize instance of '' out of START_OBJECT token
```
类似这样的。
#####解决方法：
这些错误表面上看好像是返回的数据出了问题。实际上这些错误只能证明，通过Feign调用其它服务时，被调用的服务出了错误，比如SQL错误等。
所以只要把关注点从调用方移到被调用方，在被调用服务中debug找到错误就可以了。