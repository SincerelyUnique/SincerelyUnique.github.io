---
title: oracle根据条件统计各组记录数量
date: 2019-09-16 17:08:25
tags:
- oracle
categories:
- [学习, 数据库学习, Oracle]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1.根据具体条件统计这批数据落到每组的数量 

```sql
select to_char(TIMESTAMP, 'yyyy-mm-dd') column1, column2, column3,
       count(CASE WHEN DURATION<30*60 THEN 1 END) less30,
       count(CASE WHEN DURATION>=30*60 and DURATION<60*60*1000 THEN 1 END) more30less60,
       count(CASE WHEN DURATION>=60*60 and DURATION<120*60*1000 THEN 1 END) more60less120,
       count(CASE WHEN DURATION>=120*60 and DURATION<360*60*1000 THEN 1 END) more120less360,
       count(CASE WHEN DURATION>=360*60 THEN 1 END) more360
from user_table1
where to_char(TIMESTAMP, 'yyyy-mm-dd') in ('2019-09-16', '2019-09-15')
group by to_char(TIMESTAMP, 'yyyy-mm-dd'), column2, column3;
```

2.将上面的数量merge into到另一个新的table（存在即跟新，不存在即插入）

```sql
merge into user_table2 t1
using (select to_char(TIMESTAMP, 'yyyy-mm-dd') column1,
              column2,
              column3,
              count(CASE WHEN DURATION < 30 * 60 THEN 1 END)                                     less30,
              count(CASE WHEN DURATION >= 30 * 60 and DURATION < 60 * 60 * 1000 THEN 1 END)   more30less60,
              count(CASE WHEN DURATION >= 60 * 60 and DURATION < 120 * 60 * 1000 THEN 1 END)  more60less120,
              count(CASE WHEN DURATION >= 120 * 60 and DURATION < 360 * 60 * 1000 THEN 1 END) more120less360,
              count(CASE WHEN DURATION >= 360 * 60 THEN 1 END)                                   more360
       from user_table1
       where to_char(TIMESTAMP, 'yyyy-mm-dd') in ('2019-09-16', '2019-09-15')
       group by to_char(TIMESTAMP, 'yyyy-mm-dd'), column2, column3) t2
on (t1.column1 = t2.column1 and t1.column2 = t2.column2 and t1.column3 = t2.column3)
WHEN MATCHED THEN
  update set t1.less30 = t2.less30,
    t1.more30less60    = t2.more30less60,
    t1.more60less120   = t2.more60less120,
    t1.more120less360  = t2.more120less360,
    t1.more360         = t2.more360
WHEN NOT MATCHED THEN
  INSERT (t1.column1, t1.column2, t1.column3, t1.LESS30, t1.MORE30LESS60, t1.MORE60LESS120, t1.MORE120LESS360, t1.MORE360)
  values (t1.column1, t2.column2, t2.column3, t2.less30, t2.more30less60, t2.more60less120, t2.more120less360, t2.more360);
```


