---
title: Host 'xxxxxxx' is not allowed to connect to this MySQL server
date: 2019-11-21 16:28:23
tags:
- mysql
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1.登录mysql

> mysql -u root -p

2.切换到mysql数据库

```sql
USE mysql;
SELECT user,host FROM user;
```

3.授权

```sql
GRANT ALL PRIVILEGES ON *.* TO root@my_ip IDENTIFIED BY ‘root_password‘ WITH GRANT OPTION;
```
