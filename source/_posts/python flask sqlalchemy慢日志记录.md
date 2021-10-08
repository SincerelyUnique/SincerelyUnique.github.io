---
title: python flask sqlalchemy慢日志记录
date: 2018-12-28 11:24:52
tags:
- python
- flask
- sqlalchemy
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

参考1：https://www.cnblogs.com/rgcLOVEyaya/p/RGC_LOVE_YAYA_605days.html

参考2：https://gitee.com/Green-unicorn/codes/1u8rlt03gvpic7xfwz9je12

```python
engine = create_engine(ProdConfig.SQLALCHEMY_DATABASE_URI, echo=True)
 
app = Flask(__name__)
app.config['FLASKY_DB_QUERY_TIMEOUT'] = 0.0001  # 设置sql执行超时时间，#记录执行时间超过 0.0001秒的
app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True  # 断开设置
app.config['SQLALCHEMY_RECORD_QUERIES'] = True  # 启用慢查询记录功能
 
 
from flask_sqlalchemy import get_debug_queries
 
 
@app.after_request
def after_request(response):
    for query in get_debug_queries():
        if query.duration >= app.config['FLASKY_DB_QUERY_TIMEOUT']:
            print('#####Slow query:%s \nParameters:%s \nDuration:%fs\nContext:%s\n #####' %
                  (query.statement, query.parameters, query.duration,query.context))  # 打印超时sql执行信息
    return response
 
 
@app.teardown_request
def handle_teardown_request(ex):
    db.session.remove()
```
