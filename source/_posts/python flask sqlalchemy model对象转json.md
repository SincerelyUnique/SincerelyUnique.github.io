---
title: python flask sqlalchemy model对象转json
date: 2019-01-29 10:35:26
tags:
- python
- sqlalchemy
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
def model_to_json_test():
    from flask import jsonify
    q = db.session.query(WhiteSite).first()  # db = SQLAlchemy()
    q_dict = serialize(q)
    q_json = jsonify(q_dict)
    return q_json
 
 
def serialize(model):
    from sqlalchemy.orm import class_mapper
    columns = [c.key for c in class_mapper(model.__class__).columns]
    return dict((c, getattr(model, c)) for c in columns)
```

```python
class WhiteSite(db.Model):
    __tablename__ = 'WHITE_SITE'
    site_name = db.Column(db.String(128), primary_key=True)
    comments = db.Column(db.String(1000))
    create_time = db.Column(db.DateTime, default=func.now())
    create_user = db.Column(db.String(128))
    last_modified_time = db.Column(db.DateTime, default=func.now(), onupdate=func.now())
    last_modified_user = db.Column(db.String(512))
```
