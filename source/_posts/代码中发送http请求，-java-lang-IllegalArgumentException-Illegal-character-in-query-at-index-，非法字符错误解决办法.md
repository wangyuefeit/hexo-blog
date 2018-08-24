---
title: >-
  代码中发送http请求， java.lang.IllegalArgumentException: Illegal character in query at
  index...，非法字符错误解决办法
categories:
  - Java
tags:
  - Java
  - http
  - 请求
date: 2018-08-24 15:45:27
---
产生原因：url中有汉字或特殊字符(非字母和数字的字符)，没有转码。

解决办法：将带有特殊字符或汉字的参数进行转码，再放入url发送即可。

例如：param1 = URLEncoder.encode(param1, "UTF-8");