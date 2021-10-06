---
title: A value is required for bind parameter (sqlalchemy)
date: 2019-06-26 15:37:43
tags:
- python
- sqlalchemy
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
if __name__ == '__main__':
    from sqlalchemy import create_engine
    from sqlalchemy import text
    db_url = "oracle://user:password@ip:port/?service_name=xxx"
    engine = create_engine(db_url, echo=True)
 
    connection = engine.connect()
    trans = connection.begin()
    param = '"errorCode":431335,"description":"clean_up_queue"'
    sql = "insert into Table_TEST(BUG_NO) values('{0}')".format(param).decode('ISO-8859-1')
    # connection.execute(text(sql))  # insert failure, because text() func will take ':xxx' as a parameter
    # connection.execute(sql)  # insert success, but need pay attention to sql inject
    trans.commit()
```

Reference:

https://kite.com/python/docs/sqlalchemy.text
