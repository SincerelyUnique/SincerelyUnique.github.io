---
title: MySQL 8 1251 client does no support authentic...
date: 2019-03-16 15:34:24
tags:
- mysql
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 以windows为例，cmd进入命令行，cd进入mysql安装后的bin目录，登陆mysql

![mysql8801](../images/mysql8801.png)

2. 查看用户信息

```sql
select host,user,plugin,authentication_string from mysql.user;
```

![mysql8802](../images/mysql8802.png)

3. 更新密码

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
```

4. 再次查看用户信息，并核对

![mysql8804](../images/mysql8804.png)

5. 登陆navicat for mysql

![mysql8803](../images/mysql8803.png)

参考：https://blog.csdn.net/u012604745/article/details/80632860 
