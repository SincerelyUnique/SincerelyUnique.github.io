---
title: oracle分组取前5条数据
date: 2017-12-13 09:59:04
tags:
- oracle
- sql
categories:
- [学习, 数据库学习, Oracle]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

语句如下：

```sql
 select *  
  from (select t.*,  
               (Row_number() over(partition by 分组字段 order by 排序字段)) rn  
          from 表名字 t)  
 where rn < 6;  
```

示例：

```sql
select *  
  from (select t.*,  
               (Row_number() over(partition by t.type order by t.id)) rn  
          from tbl_requisition t)  
 where rn < 6;  
```

意思是：根据tbl_requisition表中的type类型进行分组，取前5条，且这5条数据是根据id排序的

来个截图（查找结果）

![](../../../../images/part01.png)

