---
title: oracle表备份
date: 2019-07-30 11:17:01
tags:
- oracle
categories:
- [学习, 数据库学习, Oracle]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```sql
--表不存在
create table tablename_backup as select * from tablename;
 
--表已存在
truncate table tablename_backup;
insert into tablename_backup select * from tablename;
 
--数据从一张表转到另一张表
insert into table1(username, password) select username, password from table2;
```
