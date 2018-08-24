---
title: "Java | MySQL | Emoji表情写入数据库时报错问题解决，Incorrect string value: 'ð\x9F\x98\x84ð\x9F...' for column"
categories:
  - Java
tags:
  - MySQL
  - Java
  - Emoji
date: 2018-08-24 15:46:20
---
#####现象：
将Emoji表情存入数据库时，报如下错误：
```
Cause: java.sql.SQLException: Incorrect string value: '\xF0\x9F\x99\x83\xF0\x9F...' for column 'XXX' at row 1
; uncategorized SQLException; SQL state [HY000]; error code [1366]; Incorrect string value: '\xF0\x9F\x99\x83\xF0\x9F...' for column 'XXX' at row 1; nested exception is java.sql.SQLException: Incorrect string value: '\xF0\x9F\x99\x83\xF0\x9F...' for column 'XXX'
```
#####原因：
这个问题的主要原因就是MySQL使用的是ut8编码，utf8编码默认每个字符3个字节，而Emoji表情使用的Unicode编码占4个字节，所以写入数据库的时候会写入失败并报错。

#####解决办法：

######一、首先确认数据库、表、字段的编码是否是utf8m64，如果是，就跳过这一步。如果不是，先把这些字符集都改为utf8m64:
```
# 修改数据库:
ALTER DATABASE database_name CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci;
# 修改表:
ALTER TABLE table_name CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
# 修改表字段:
ALTER TABLE table_name CHANGE column_name column_name VARCHAR(191) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
######二、然后解决代码。代码中有两种解决办法：
1. 针对需要的字段改：
在Dao层加一个方法：
```
@Update("set names utf8mb4")  
public void setCharsetToUtf8mb4();  
```
&nbsp;&nbsp;&nbsp;&nbsp;然后调用写入方法之前，先调用此方法即可。
2. 一劳永逸，在Java配置数据库连接池的地方加上一行代码即可：
```
@Bean(name = "dataSource")
@Primary
public DataSource dataSource() {
    DruidDataSource dataSource = new DruidDataSource();
    dataSource.setDriverClassName(driverClass);
    dataSource.setUrl(url);
    dataSource.setUsername(user);
    dataSource.setPassword(password);
    dataSource.setConnectionInitSqls(Arrays.asList("set names utf8mb4")); # 加入此行代码即可
    dataSource.setInitialSize(initialSize);
    dataSource.setMinIdle(minIdle);
    dataSource.setMaxActive(100);
    dataSource.setMaxWait(maxWait);
    dataSource.setTimeBetweenEvictionRunsMillis(timeBetweenEvictionRunsMillis);
    dataSource.setValidationQuery(validationQuery);
    dataSource.setTestWhileIdle(testWhileIdle);
    dataSource.setTestOnBorrow(testOnBorrow);
    return dataSource;
}
```
这样就可以把Emoji表情写入数据库了，并且可以成功的读取出来。