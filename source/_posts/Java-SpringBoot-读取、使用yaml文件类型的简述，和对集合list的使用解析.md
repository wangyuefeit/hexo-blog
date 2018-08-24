---
title: Java SpringBoot 读取、使用yaml文件类型的简述，和对集合list的使用解析
categories:
  - Java
tags:
  - Java
  - SpringBoot
  - yaml
  - list
date: 2018-08-24 15:47:06
---
#####基本类型的使用
yml文件大部分使用的都是字符串，如果想使用其它类型，只要直接按其它类型写变量值就可以了。
举例：
```
#使用boolean
my-switch:
 is-on: true
```
Java中使用只要加上@Value就可以了：
```
@Value("${my-switch.is-on}")
private boolean switchOn;
```
使用其它类型也是一样的。
***
#####集合的使用
举例：
```
#使用int的list
student:
 ids: [1, 2, 3, 4, 5]
```
或者：
```
#使用int的list
student:
 ids: 
  - 1
  - 2
  - 3
  - 4
  - 5
```
这个时候要注意了，Java如果直接写成：
```
@Value("${student.id}")
private List<Integer> ids;
```
启动时会报错，Cannot resolve placeholder 什么的
这时候应该新建一个对list属性的配置类：
```
@Configuration
@ConfigurationProperties("student")
public class PropertyConfig{
  private List<Integer> ids;
  // getter & setter
}
```
然后在要使用的地方自动注入，调用一个getter方法就可以得到配置文件中的值。
***
yml文件还可以存放对象和对象的集合，使用方法与基本类型类似。
简单举例：
```
#使用对象
student:
  id: 1
  name: Bruce
  gender: male
```
```
#使用对象集合
students: 
  - id: 1
    name: Bruce
    gender: male
  - id: 2
    name: ...
    ...
```