---
title: oracle merge into（一条语句执行更新或插入）
date: 2019-09-03 16:50:50
tags:
- sql
- oracle
categories:
- [学习, 数据库学习, Oracle]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```sql
MERGE INTO user_tab1 t1
USING (select to_char(TIMESTAMP, 'yyyy-mm-dd') daily, CLUSTERNAME, RESULT, count(*) usage
       from user_tab2
       where to_char(TIMESTAMP, 'yyyy-mm-dd') in ('2019-09-03')
       group by to_char(TIMESTAMP, 'yyyy-mm-dd'), CLUSTERNAME, RESULT) t2
on (t1.DAILY = t2.daily and t1.CLUSTERNAME = t2.CLUSTERNAME and t1.RESULT = t2.RESULT)
WHEN MATCHED THEN 
    UPDATE SET t1.USAGE = t2.usage
WHEN NOT MATCHED THEN 
    INSERT (t1.daily, t1.CLUSTERNAME, t1.RESULT, t1.usage)
    VALUES (t2.daily, t2.CLUSTERNAME, t2.RESULT, t2.usage);
```
