---
title: logging gunicorn with APScheduler in flask
date: 2019-02-26 15:17:50
tags:
- gunicorn
- python
- flask
- logger
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 使用pycharm创建一个flask项目；

2. 将下面代码覆盖到项目中的app.py； 

```python
from flask import Flask
from flask_apscheduler import APScheduler
import logging
 
logging.basicConfig(level=logging.DEBUG, format='%(asctime)s %(levelname)s %(message)s')
handler = logging.StreamHandler()
handler.setLevel(logging.DEBUG)
 
logger = logging.getLogger(__name__)
logger.addHandler(handler)
logger.handlers.extend(logging.getLogger("gunicorn.error").handlers)
 
 
class Config(object):
    JOBS = [{
        'id': 'job1',
        'func': 'app:job1',
        'args': (1, 2),
        'trigger': 'interval',
        'seconds': 10
    }]
 
    SCHEDULER_API_ENABLED = True
 
 
def job1(a, b):
    logger.info(str(a) + ' ' + str(b))
 
 
app = Flask(__name__)
app.config.from_object(Config())
scheduler = APScheduler()
scheduler.init_app(app)
scheduler.start()
 
 
if __name__ == '__main__':
    app.run()
```

3. 将项目压缩一下，放到linux服务器（因为gunicorn仅支持unix系统）；

4. 解压后，在项目路径下创建logs文件夹，并使用如下命令运行：

```
gunicorn --bind 0.0.0.0:8080 --workers 1 --access-logfile logs/access_log --error-log logs/error_log app:app
```

5. 可以看到生成的error_log有日志输出

```log
[2019-02-26 07:05:11 +0000] [10643] [INFO] Starting gunicorn 19.5.0
[2019-02-26 07:05:11 +0000] [10643] [INFO] Listening at: http://0.0.0.0:8080 (10643)
[2019-02-26 07:05:11 +0000] [10643] [INFO] Using worker: sync
[2019-02-26 07:05:11 +0000] [10647] [INFO] Booting worker with pid: 10647
[2019-02-26 07:05:21 +0000] [10647] [INFO] 1 2
[2019-02-26 07:05:31 +0000] [10647] [INFO] 1 2
```
