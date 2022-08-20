---
title: tomcat添加用户(linux)
date: 2016-09-20 10:13:11
tags:
- tomcat
categories:
- [学习, DevOPS, Tomcat]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
<role rolename="tomcat"/>
  <role rolename="role1"/>
  <user password="tomcat" roles="tomcat" username="tomcat"/>
  <user password="tomcat" roles="tomcat,role1" username="both"/>
  <user password="tomcat" roles="role1" username="role1"/>
  <role rolename="manager-gui"/>
<user password="s3cret" roles="manager-gui" username="tomcat"/>
```
