---
title: >-
  MyBatis异常: There is no getter for property named 'XXX' in 'class
  java.lang.String'
categories:
  - Java
tags:
  - MyBatis
  - Java
  - String
  - 异常
date: 2018-08-24 15:48:31
---
#####现象：
MyBatis的mapper.xml文件传入String类型参数，并且对该参数进行了判空，在执行到这条SQL的时候，就会报这个错误。
例如：
```
<select id="countStudent" resultType="int">
        SELECT COUNT(*) 
        FROM table_student 
        <where>
            <if test="null != stuName and '' != stuName">
                AND stu_name LIKE #{stuName}
            </if>
        </where>
</select>
```
#####解决：
将test中的参数名称改为“_parameter”即可（SQL语句中的参数名称可以不改）。
如下：
```
<select id="countStudent" resultType="int">
        SELECT COUNT(*) 
        FROM table_student 
        <where>
            <if test="null != _parameter and '' != _parameter">
                AND stu_name = #{stuName}
            </if>
        </where>
</select>
```