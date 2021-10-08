---
title: The used select statements have a different number of columns解决办法
date: 2018-08-03 17:28:05
tags:
- sql
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 问题原因 

这个异常出现在两个表使用union all进行合并时遇到的，

表A存在的字段数量和表B最初是一致的，包括字段名字和类型都是一致，使用union all连接无异常；

后来由于需要在表B添加了一个新的字段，导致两张表的字段在数量上不一致，出现了异常；

# 解决办法

在使用union all进行合并操作时，使用null as "xxx"解决，如下，由之前的SQL语句

```sql
SELECT *
	FROM project t
LEFT JOIN (
	SELECT * FROM biz_a_contract
	UNION ALL
	SELECT * FROM biz_b_contract
) a ON a.id = t.contract_id
WHERE
	t.id = '200'
```

改为

```sql
SELECT *
	FROM project t
LEFT JOIN (
	SELECT * FROM biz_a_contract
	UNION ALL
	SELECT tt.*,null AS 'xx' FROM biz_b_contract tt
) a ON a.id = t.contract_id
WHERE
	t.id = '200'
```

或者直接列出具体的字段，保证顺序一致性
