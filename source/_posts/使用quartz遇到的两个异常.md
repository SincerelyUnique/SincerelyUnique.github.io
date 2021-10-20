---
title: 使用quartz遇到的两个异常
date: 2017-09-18 15:20:50
tags:
- quartz
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 异常1
```
Caused by:
       java.lang.NoClassDefFoundError: org/springframework/transaction/TransactionException
	at java.lang.Class.getDeclaredConstructors0(Native Method)
	at java.lang.Class.privateGetDeclaredConstructors(Class.java:2389)
	at java.lang.Class.getConstructor0(Class.java:2699)
	at java.lang.Class.getDeclaredConstructor(Class.java:1985)
        .....
Caused by: java.lang.ClassNotFoundException: org.springframework.transaction.TransactionException
	at org.apache.catalina.loader.WebappClassLoader.loadClass(WebappClassLoader.java:1711)
	at org.apache.catalina.loader.WebappClassLoader.loadClass(WebappClassLoader.java:1556)
	... 29 more
```
解决：添加spring-tx
```xml
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>4.3.8.RELEASE</version>
    </dependency>
```
# 异常2
```
Caused by: java.lang.NoClassDefFoundError: org/quartz/impl/triggers/CronTriggerImpl
	at java.lang.Class.getDeclaredMethods0(Native Method)
	at java.lang.Class.privateGetDeclaredMethods(Class.java:2701)
	at java.lang.Class.getDeclaredMethods(Class.java:1975)
	at org.springframework.util.ReflectionUtils.getDeclaredMethods(ReflectionUtils.java:613)
	at org.springframework.util.ReflectionUtils.doWithMethods(ReflectionUtils.java:524)
	at org.springframework.util.ReflectionUtils.doWithMethods(ReflectionUtils.java:510)
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor.determineCandidateConstructors(AutowiredAnnotationBeanPostProcessor.java:247)
	... 71 more
Caused by: java.lang.ClassNotFoundException: org.quartz.impl.triggers.CronTriggerImpl
	at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1333)
	at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1167)
	... 78 more
```
解决：quartz版本问题，升级，由之前的1.8.5升级到2.2.3（最新）
```xml
    <dependency>
      <groupId>org.quartz-scheduler</groupId>
      <artifactId>quartz</artifactId>
      <version>2.2.3</version>
    </dependency>
```
