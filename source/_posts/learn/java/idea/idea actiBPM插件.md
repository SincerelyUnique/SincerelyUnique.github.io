---
title: idea actiBPM插件
date: 2018-06-23 16:49:28
tags:
- ide
- activiti
categories:
- [学习, Java语言学习, IntelliJIDEA]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

idea actiBPM插件

安装问题

1. 访问https://plugins.jetbrains.com/，下载actiBPM.jar
2. ctrl+alt+s，选plugins，选择从硬盘安装
3. restart，右键，new bpmnFile.

乱码问题

1. Config the startup IDEA options(idea.exe.vmoptions or idea64.exe.vmoptions), add -Dfile.encoding=UTF-8
2. After start IDEA, set Settings—>Editor—>File Encodings to UTF-8

如果上述操作引起idea console日志中文乱码,可设置服务器启动参数：-Dfile.encoding=UTF-8

不过设置完tomcat vm启动参数后，有可能导致mysql报空指针异常，额，详情参考mysql官网有人提出的一个NPE bug

地址: https://bugs.mysql.com/bug.php?id=72630

所以还是暂时不用actiBPM插件了
