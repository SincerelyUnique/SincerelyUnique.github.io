---
title: python codec issue(2)
date: 2019-10-22 10:54:01
tags:
- python
- codec
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1.报错代码

```python
df_result = pd.read_sql_query(sql, engine)
```

2.错误跟踪（还没有找到问题）

```
site-packages\pandas\io\sql.py, line 314
site-packages\pandas\io\sql.py, line 1108
site-packages\sqlalchemy\engine\result.py, line 1216
site-packages\sqlalchemy\engine\base.py, line 1475
site-packages\sqlalchemy\engine\result.py, line 1211
site-packages\sqlalchemy\engine\result.py, line 1161
 
（1）error code经过两个modules （pandas和sqlalchemy，报错位置为SQLAlchemy模块）
    pandas             0.24.2
    SQLAlchemy         1.3.10
（2）看一下SQLAlchemy模块底层怎么实现的，可以debug进去，一步步调试
```

3.报错原因

```
-- 查询Oracle DB编码为WE8ISO8859P1 （即ISO-8859-1，拉丁字符集）
select * from nls_database_parameters where parameter ='NLS_CHARACTERSET';
 
-- 本地NLS_LANG变量配置为AMERICAN_AMERICA.WE8ISO8859P1，与DB配置一致
 
-- 本地Python环境编码为utf-8
 
-- 因为DB里有拉丁字符集里的不间断空白符（也即编码后地址为0xa0），导致utf-8无法decode
```

4.解决办法

```
# sqlalchemy降级
pip install SQLAlchemy==1.2.6
```

5.参考

```
（1）sqlalchemy changelog： https://docs.sqlalchemy.org/en/13/changelog/
（2）哪些版本涉及编码的修改：
        SQLAlchemy1.2.16  
       （https://docs.sqlalchemy.org/en/13/changelog/changelog_12.html#change-1.2.16）
        SQLAlchemy1.3.0b1 use_nchar_for_unicode=True   
       （https://docs.sqlalchemy.org/en/13/changelog/changelog_13.html#change-1.3.0b1-oracle）
        SQLAlchemy1.3.0b2      
       （https://docs.sqlalchemy.org/en/13/changelog/changelog_13.html#change-1.3.0b2-sql）
        SQLAlchemy1.3.11  
       （https://docs.sqlalchemy.org/en/13/changelog/changelog_13.html#change-1.3.11-oracle）
```

6.参考我提的一个Github Issue，可以等待作者发布1.3.11版本，在create_engine()时携带一个参数

https://github.com/sqlalchemy/sqlalchemy/issues/4932

