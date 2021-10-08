---
title: java.lang.UnsupportedClassVersionError
date: 2018-02-28 08:51:34
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

顾名思义：意思是“不支持的类版本错误”。


产生原因：

Linux服务器上项目是使用jdk1.6编译的，而我本地项目却是使用1.7编译的。

在向服务器上替换源文件时会遇到此异常，

异常显示：

![exce01](../images/exce01.png)
