---
title: 'Java,SpringMVC返回值中含有等号=返回后的JSON变成\u003d的问题'
categories:
  - Java
tags:
  - Java
  - JSON
date: 2018-08-24 15:51:23
---
比如返回一个url：http://***.******.com/abc?a=1&b=2

会变成：http://***.******.com/abc?a\u003d&b\u003d2

这是Gson初始化的问题，默认会对某些特殊字符进行ISO8859-1编码。参照下面写法，便可解除诅咒：
```
Gson gson = new GsonBuilder().disableHtmlEscaping().create();
```

然后再用gson去toJson等，就不会自动编码了。