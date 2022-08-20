---
title: sqlalchemy批量插入（存在唯一约束）
date: 2019-05-06 12:21:31
tags:
- python
- sqlalchemy
categories:
- [学习, Python语言学习, SQLAlchemy]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

插入oracle时，如果某个列存在唯一约束，同时批量插入时数据重复，会报error，此时将error的过滤掉，最后commit

```python
def insert_one_by_one():
    from sqlalchemy import create_engine
    engine = create_engine("oracle://user:pass@xx.xx.xx.xx:xxxx/?service_name=xxx.xxx.xxx", echo=True)
 
    user_list = [{'username': 'jalen1', 'age': '11', 'height': '123'},
                 {'username': 'jalen2', 'age': '12', 'height': '124'},
                 {'username': 'jalen3', 'age': '13', 'height': '125'},
                 {'username': 'jalen4', 'age': '14', 'height': None}]
 
    conn = engine.connect()
    trans = conn.begin()
    try:
        for user in user_list:
            try:
                sql = "INSERT INTO tb_user(username, age, height) VALUES('{0}', '{1}', '{2}')".\
                    format(user['username'], user['age'], user['height'])
                conn.execute(sql)
            except Exception as e:
                logger.info(e.message)
        trans.commit()
    except Exception:
        trans.rollback()
        raise
    finally:
        conn.close()
```
