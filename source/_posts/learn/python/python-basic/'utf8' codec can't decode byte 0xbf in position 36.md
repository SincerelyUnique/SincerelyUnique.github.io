---
title: python codec issue(3)
date: 2019-08-22 13:38:43
tags:
- python
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

这里只记录一种原因

1.报错代码

```python
from sqlalchemy import create_engine
engine = create_engine("oracle://xxx:xxx@xxx:xxx/?service_name=xxx", echo=True)
import pandas as pd
 
source_bugs = "select * from xxxx"
df_source_bugs = pd.read_sql_query(source_bugs, engine)
```

2.Traceback 

```python
[2019-08-22 13:33:28,289] ERROR in app: Exception on /bug_management/list [GET]
Traceback (most recent call last):
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\flask\app.py", line 1982, in wsgi_app
    response = self.full_dispatch_request()
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\flask\app.py", line 1614, in full_dispatch_request
    rv = self.handle_user_exception(e)
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\flask\app.py", line 1517, in handle_user_exception
    reraise(exc_type, exc_value, tb)
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\flask\app.py", line 1612, in full_dispatch_request
    rv = self.dispatch_request()
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\flask\app.py", line 1598, in dispatch_request
    return self.view_functions[rule.endpoint](**req.view_args)
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\dashboard\bugmanage\views.py", line 48, in bug_manage_table
    df_source_bugs = pd.read_sql_query(source_bugs, engine)
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\pandas\io\sql.py", line 314, in read_sql_query
    parse_dates=parse_dates, chunksize=chunksize)
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\pandas\io\sql.py", line 1072, in read_query
    data = result.fetchall()
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\sqlalchemy\engine\result.py", line 1216, in fetchall
    e, None, None, self.cursor, self.context
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\sqlalchemy\engine\base.py", line 1468, in _handle_dbapi_exception
    util.reraise(*exc_info)
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\sqlalchemy\engine\result.py", line 1211, in fetchall
    l = self.process_rows(self._fetchall_impl())
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\sqlalchemy\engine\result.py", line 1161, in _fetchall_impl
    return self.cursor.fetchall()
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\site-packages\sqlalchemy\processors.py", line 74, in process
    return decoder(value, errors)[0]
  File "C:\Users\jalchu\Documents\cloudtools-stap\provision\venv\lib\encodings\utf_8.py", line 16, in decode
    return codecs.utf_8_decode(input, errors, True)
UnicodeDecodeError: 'utf8' codec can't decode byte 0xbf in position 36: invalid start byte
```

3.原因

SQLAlchemy版本原因（根本原因是python版本原因，因为python2和python3的编码有所不同，所以一些三方框架对于版本升级后的兼容性未做处理）

参考：

https://docs.sqlalchemy.org/en/13/changelog/migration_13.html

https://docs.sqlalchemy.org/en/13/changelog/migration_13.html#change-4393-convertunicode

4.解决

将SQLAlchemy版本由1.3.7降为1.2.6

pip install SQLAlchemy==1.2.6
