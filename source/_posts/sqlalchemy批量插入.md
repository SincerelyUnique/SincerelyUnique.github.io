---
title: sqlalchemy批量插入
date: 2019-05-05 17:03:50
tags:
- python
- sqlalchemy
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
def batch_insert_test():
    from sqlalchemy import create_engine, Table, Column, func, String, MetaData
    import pandas as pd
    import logging
    engine = create_engine("oracle://user:pass@x.x.x.x:xxxx/?service_name=xxx.xxx.xxx", echo=True)
 
    # Temp list
    user_list = [{'mingzi': 'jalen1', 'nianling': '11', 'tizhong': '123'},
                 {'mingzi': 'jalen2', 'nianling': '12', 'tizhong': '124'},
                 {'mingzi': 'jalen3', 'nianling': '13', 'tizhong': '125'},
                 {'mingzi': 'jalen4', 'nianling': '14', 'tizhong': None}]
 
    conn = engine.connect()
    trans = conn.begin()
    try:
        metadata = MetaData()
        tb_user = Table('tb_user', metadata,
                        Column('username', String(32), default=func.sys_guid(), primary_key=True),
                        Column('age', String(64)),
                        Column('height', String(64))
                        )
 
        # Rename <list key> to <db column name>
        df_users = pd.DataFrame(user_list)
        rename_columns = {'mingzi': 'username', 'nianling': 'age', 'tizhong': 'height'}
        df_users = df_users.rename(index=str, columns=rename_columns).fillna('')
        users = df_users.to_dict('records')
 
        # Batch insert
        conn.execute(tb_user.insert(), users)
        trans.commit()
    except Exception as e:
        trans.rollback()
        logging.info('Method Exception: ' + e.message)
        logging.info(traceback.format_exc())
    finally:
        conn.close()
```

参考：https://docs.sqlalchemy.org/en/13/core/tutorial.html#executing-multiple-statements
