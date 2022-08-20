---
title: oracle.sql.TIMESTAMP类型转成固定格式的date类型
date: 2017-09-18 11:29:46
tags:
- oracle
- sql
categories:
- [学习, 数据库学习, Oracle]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

今天做Excel导出，需要从数据库导出一个时间类型的字段create_time

然后就遇到一个oracle的时间类型转换的问题，不进行转换的话，导出的时间类似于这样`oracle.sql.TIMESTAMP@57c8f82b`

在数据库中，create_time这个字段的类型为`TIMESTAMP(6)`

![](../../../../images/timestamp01.png)  ![](../../../../images/timestamp02.png)

因为我们是SSM框架，所以从映射文件做一下转换

![](../../../../images/timestamp03.png)

此时我们执行这条sql就会看到如下结果（我是用idea的debug模式查看的）

![](../../../../images/timestamp04.png)
