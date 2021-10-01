---
title: Working outside of application context
date: 2019-08-28 12:27:32
tags:
- python
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

错误代码：

![outside01](../images/outside01.png)


原因：没有激活程序上下文之前就调用current_app.name会导致该错误

修改代码：

![outside02](../images/outside02.png)
