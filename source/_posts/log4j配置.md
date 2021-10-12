---
title: log4j配置
date: 2017-11-24 12:43:53
tags:
- log4j
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
###Setting###
log4j.rootLogger = debug,stdout,D,E,I
###Output to console###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n
###Output debug to debug.log###
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = /home/jalenchu/jalen_chu/logs/debug.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = DEBUG 
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
###Output info to info.log###
log4j.appender.I = org.apache.log4j.DailyRollingFileAppender
log4j.appender.I.File = /home/jalenchu/jalen_chu/logs/info.log
log4j.appender.I.Append = true
log4j.appender.I.Threshold = INFO 
log4j.appender.I.layout = org.apache.log4j.PatternLayout
log4j.appender.I.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
###Output error to error.log###
log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File =/home/jalenchu/jalen_chu/logs/error.log
log4j.appender.E.Append = true
log4j.appender.E.Threshold = ERROR 
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
```
