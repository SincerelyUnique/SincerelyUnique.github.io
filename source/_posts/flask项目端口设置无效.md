---
title: flask项目端口设置无效
date: 2018-10-30 16:17:12
tags:
- python
- flask
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 程序设置（该方法无效）

![invalid01](../images/invalid01.png)

解决方法：

右上角打开 "Edit Configurations" ，在Additional Options处设置启动参数

![invalid02](../images/invalid02.png)

删掉之前在run方法中设置的参数，再次运行OK

![invalid03](../images/invalid03.png)
