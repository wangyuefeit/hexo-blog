---
title: 在MarkdownPad2中实现代码块换行的几种简单方式
date: 2018-08-24 14:43:31
categories: 
	- PC使用
tags: 
	- Markdown
	- MarkdownPad2
	- 代码块
	- 换行
---

在MarkdownPad2中编写.md文档时，如果直接用\``` ```这样的标签来标示代码块的话，代码会呈现很长一串，而自动忽略了本来的换行符。解决这个问题的方法暂时收集到了3个，如下：

1. 方法一，对MarkdownPad2进行设置：
在MarkdownPad2中点击菜单栏的Tools >> Options >> Markdown，将右边的Markdown Processor设置为CommonMark，然后保存并关闭。

![](https://upload-images.jianshu.io/upload_images/10709282-4593622a646aea85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 方法二，使用Tab格式化代码块：

这个方法先将代码块上下的格式化符号去掉，比如\```这些，去掉之后选中代码，按一下键盘上的Tab键，即可格式化代码块。

3. 方法三，使用\<pre>\</pre>来标示代码块。