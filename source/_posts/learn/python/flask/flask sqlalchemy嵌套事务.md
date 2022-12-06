---
title: flask sqlalchemy嵌套事务
date: 2022-11-29 16:17:12
tags:
- python
- flask
categories:
- [学习, Python语言学习, Flask]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> 背景：一个存储操作，存储时涉及多张表的数据录入，期望是如果某张表录入出错，多张表同时回滚，这里使用嵌套事务

# 添加事务处理方法

```python
from contextlib import ContextDecorator


def atomic(db):
    if callable(db):
        return Atomic(db)(db)
    else:
        return Atomic(db)


class Atomic(ContextDecorator):

    def __init__(self, db):
        self.db = db

    def __enter__(self):
        pass

    def __exit__(self, exc_typ, exc_val, tb):
        if exc_typ:
            self.db.session.rollback()
        else:
            self.db.session.commit()
```


# 在需要做嵌套事务的方法中调用上述方法

```python
from dashboard.model.common import atomic
# db = SQLAlchemy()
# logging.basicConfig(level=logging.INFO, format="%(asctime)s %(process)d:%(thread)d %(filename)s:%(lineno)d:%(levelname)s:%(message)s")
# logger = logging.getLogger(__name__)
from dashboard import logger, db


def add_data():
    logger.info("user save data")
    with atomic(db):
        xxxbiz.add_data1(data)
        xxxbiz.add_data2(data)
        xxxbiz.add_data3(data)
        xxxbiz.add_data4(data)
        xxxbiz.add_data5(data)
    logger.info("save data success")
```

# 说明

## 关于with语句的说明

请参考：https://peps.python.org/pep-0343/
