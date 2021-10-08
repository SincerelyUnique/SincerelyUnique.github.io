---
title: mysql严格模式
date: 2018-06-21 09:38:51
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

严格模式说明参考：http://blog.sina.com.cn/s/blog_53b13d950100x2ok.html

```
set @@sql_mode='STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
set @@global.sql_mode='STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
SELECT @@sql_mode;
select @@global.sql_mode;
```

上面是临时的，如果永久需修改

windows：C:\ProgramData\MySQL\MySQL Server 5.7

linux：安装目录下my.cnf
