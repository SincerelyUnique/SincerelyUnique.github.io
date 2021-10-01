---
title: oracle取带有小数的记录
date: 2019-09-06 13:45:51
tags:
- oracle
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```sql
select * from user_tab where user_column<>round(user_column,1);
```

round函数测试：

```sql
select round(100.1, 0) from dual; -- 100
select round(100.5, 0) from dual; -- 101
select round(100.9, 0) from dual; -- 101
select round(100.01, 1) from dual; -- 100
select round(100.05, 1) from dual; -- 100.1
select round(100.09, 1) from dual; -- 100.1
select round(101, -1) from dual; -- 100
select round(105, -1) from dual; -- 110
select round(109, -1) from dual; -- 110
select round(110, -2) from dual; -- 100
select round(150, -2) from dual; -- 200
select round(190, -2) from dual; -- 200
```
