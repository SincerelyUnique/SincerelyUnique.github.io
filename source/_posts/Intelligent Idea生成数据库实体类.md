---
title: Intelligent Idea生成数据库实体类
date: 2018-02-28 14:58:48
tags:
- ide
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 数据源配置：View -> Tool Windows ->  Database （或者直接右侧边栏可见）

2. 在Database窗口点击“+”添加，选择你的数据库类型，弹出窗口，添加DB地址、端口、用户名、密码并测试连接

3. 连接正常后确定，双击灰色的“Schemas...”，选择schema，一般选Current Schema

4. 数据源持久化本地配置：View -> Tool Windows ->  Persistence （可能没有）

5. 如果没有Persistence，配置Hibernate作为持久方案：File -> Project Structure... -> Modules -> "+" -> Hibernate （我的项目中未用到Hibernate，不过没关系）

6. 在Persistence窗口选中项目右键，选择Generate Persistence Mapping -> by database schema

7. 选中刚配好的datasource，选择生成实体的包路径，选择当前schema中表，点击OK生成



图文配置请参考：http://blog.csdn.net/qq_27435059/article/details/52494623

没有Persistence请参考：http://blog.csdn.net/u010267491/article/details/52815652
