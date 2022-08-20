---
title: pandas数据库查询及导出乱码问题
date: 2019-03-29 14:37:15
tags:
- python
- sqlalchemy
categories:
- [学习, Python语言学习, Pandas]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 场景：执行以下查询，查看pycharm Sciview，可以看到乱码，因为查询结果有西欧语言（如：ÿum）

> df_rows = pd.read_sql_query(sql, engine)

2. 原因

（1）查看DB字符集，结果是：WE8ISO8859P1  （ISO8859）

```sql
select * from nls_database_parameters where parameter ='NLS_CHARACTERSET';
```

（2）查看当前默认字符集为，结果为：ASCII

```python
>>> import sys
>>> sys.getdefaultencoding()
'ascii'
```

（3）原因很明显，ISO-8859-1对应为西欧语言，范围大于ASCII（参考：https://zh.wikipedia.org/wiki/ISO/IEC_8859-1），导致乱码

3. 解决：可以在执行DB查询后为该列解码

```python
df_rows['description'] = df_rows['description'].apply(lambda x: str(x).decode('ISO-8859-1'))
```

4. 由于ISO-8859-1向下兼容ASCII, 使用json直接解析时也会异常，由于ASCII不足以解析0xff范围内字符，需编码

```python
# 错误的json解析方式
json.loads(df_rows.to_json(orient='records'))  
# 正确的json解析方式
json.loads(df_rows.to_json(orient='records'), encoding='ISO-8859-1')
```
