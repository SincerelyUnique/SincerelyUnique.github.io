---
title: 控制ssh客户端login数量
date: 2024-01-02 14:34:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```shell
vi /etc/security/limits.conf


*                -       maxlogins       8
```