---
title: 将一个表中某个字段更新（copy）到另一张表
date: 2019-06-12 12:05:11
tags:
- sql
categories:
- [学习, 数据库学习, Oracle]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

场景：两张表有一个共有的字段关联，比如说A表，B表都有一个关联的id，现在想根据id，将A表的name字段更新到B表的name字段上

SQL：

```sql
update tab1
set val = (select val
           from tab2
           where tab1.id = tab2.id)
where exists(select 1 from tab2 where tab1.id = tab2.id);
```

参考：

https://blog.csdn.net/aiynmimi/article/details/53993803
