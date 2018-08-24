---
title: >-
  项目半夜凌晨宕机，报错Invalid character found in method name. HTTP method names must be
  tokens|Invalid character found in the HTTP protocol
categories:
  - Java
tags:
  - Java
  - 宕机
date: 2018-08-24 15:44:05
---
这几天项目总是在凌晨挂机，查看日志发现以下错误：

```
Http11Processor - Error parsing HTTP request header
 Note: further occurrences of HTTP header parsing errors will be logged at DEBUG level.
java.lang.IllegalArgumentException: Invalid character found in method name. HTTP method names must be tokens
        at org.apache.coyote.http11.Http11InputBuffer.parseRequestLine(Http11InputBuffer.java:426)
        at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:687)
        at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
        at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:790)
        at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1459)
        at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
        at java.lang.Thread.run(Thread.java:745)

Http11Processor - Error parsing HTTP request header
 Note: further occurrences of HTTP header parsing errors will be logged at DEBUG level.
java.lang.IllegalArgumentException: Invalid character found in the HTTP protocol
        at org.apache.coyote.http11.Http11InputBuffer.parseRequestLine(Http11InputBuffer.java:533)
        at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:687)
        at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
        at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:790)
        at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1459)
        at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
        at java.lang.Thread.run(Thread.java:745)
```

那么可能需要通过以下两种方式解决：

一、Tomcat的header缓冲区大小不够,只需要在server.xml中增加maxHttpHeaderSize字段即可:
```
<Connector URIEncoding="UTF-8" port="8080" protocol="org.apache.coyote.http11.Http11NioProtocol"
              useBodyEncodingForURI="false"
              enableLookups="false"
              connectionTimeout="20000"
              redirectPort="8443" maxHttpHeaderSize="你想要的大小"/>
```
   如果是SpringBoot项目，则需要在application.yml文件中，进行如下修改：
```
server:
  port: 项目端口
  # 下面这个参数是为解决问题而新增的
  tomcat:
    max-http-header-size:8192
```
二、可能是因为请求协议不对，需要统一成https或http请求。