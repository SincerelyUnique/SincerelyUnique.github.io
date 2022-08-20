---
title: oracle列varchar转clob
date: 2019-07-30 11:02:58
tags:
- oracle
- sql
categories:
- [学习, 数据库学习, Oracle]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```sql
SELECT * FROM tablename;
 
ALTER TABLE tablename RENAME COLUMN CONDITION TO condition_backup; --修改列名
ALTER TABLE tablename ADD condition clob NULL; --新增列
UPDATE tablename set condition=condition_backup; --数据copy到新列, 注意commit
ALTER TABLE tablename DROP COLUMN CONDITION_BACKUP; --删除旧列
 
SELECT * FROM tablename;
```
