---
title: oracle call vs exec[ute]
date: 2019-08-19 15:08:04
tags:
- oracle
categories:
- [学习, 数据库学习, Oracle]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

Both EXEC[ute] SP() and CALL SP() could be used in SQL*Plus to execute an SP. BTW, you can also use BEGIN SP(); END;

But there are some differences.

1. CALL is Oracle SQL and should work everywhere. Other DB clients that can talk to Oracle may or may not support SQL*Plus EXEC. Many do (for example, Oracle SQL Developer, SQLWorkbench/J), but some don't (Liquibase).

2. The data types of the parameters passed by the CALL statement must be SQL data types. They cannot be PL/SQL-only data types such as BOOLEAN.

3. EXEC could be used to execute not only an SP, but an arbitrary statement.

4. If an SP does not have parameters, you can use EXEC SP; syntax, but CALL requires empty parentheses: CALL SP();

python:

```python
def get_checked_data(param1, param2, param3):
    from sqlalchemy import create_engine
    import pandas as pd
    import logging
    engine = create_engine("oracle://user:pass@ip:port/?service_name=?", echo=True)
    
    logging.info('Run procedure and get data... ...')
    connection = engine.raw_connection()
    try:
        cursor = connection.cursor()
        cursor.callproc("procedure_name", [param1, param2])
        sql = "select * from table_name where username=:param3"
        df = pd.read_sql_query(sql, engine, params=[param3])
        cursor.close()
        connection.commit()
        return df.to_dict(orient='records')
    except Exception as e:
        logging.error('Get checked data for bov error:' + str(e.message))
        logging.error(traceback.format_exc())
    finally:
        connection.close()
```

