---
title: pandas to_sql插入过慢
date: 2019-06-26 15:50:41
tags:
- python
- pandas
categories:
- [学习, Python语言学习, Pandas]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

插入时指定dtype，一般为varchar，长度写大一点

```python
def set_d_type_dict(df):
    type_dict = {}
    for i, j in zip(df.columns, df.dtypes):
        if "object" in str(j):
            type_dict.update({i: VARCHAR(512)})
        if "float" in str(j):
            type_dict.update({i: DECIMAL(19, 2)})
        if "int" in str(j):
            type_dict.update({i: DECIMAL(19)})
    return type_dict
```

```python
d_type = set_d_type_dict(df)
df.to_sql('table_name', engine, if_exists='append', index=False, dtype=d_type)
```

参考：

http://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_sql.html#pandas.DataFrame.to_sql

https://blog.csdn.net/chenKFKevin/article/details/72911525

