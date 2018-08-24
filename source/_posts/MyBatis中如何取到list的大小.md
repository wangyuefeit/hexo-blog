---
title: MyBatis中如何取到list的大小
date: 2018-08-24 15:33:38
categories: 
	- Java
tags: 
	- MyBatis
	- list
---
需求：使用MyBatis进行开发时，在一个SQL语句中需要拼接list的大小。

大家都知道，当我们在MyBatis中写SQL时，如果需要遍历list，先对list进行非空判断的时候，可以加下面这行：
```
<if test="null != list and list.size != 0">SQL</if>
```
但是如果想在SQL中取到list.size的值，则比较麻烦。一般会想到如下：
```
<select id="getArticleIdByTags" parameterType="java.util.List" resultType="java.lang.Integer">
    SELECT id
    FROM table_name
    where 1=1
    <foreach collection="list" item="item" open="AND" separator="OR" close="GROUP BY id HAVING COUNT(*) >=">
        field1 = #{item.a} AND field2 = #{item.b}
    </foreach>
    #{list.size}
</select>
```
但是你会发现报错了，PersistenceException、UnsupportOperationException。

因为list.size用#{}是取不出来的，需要用${}，把#换成$就可以了。

\#{}能够有效防止SQL注入，但是也有它的缺点，它会把传入的数据自动加上一个双引号，所以如果要的是数字的话，就会比较尴尬。