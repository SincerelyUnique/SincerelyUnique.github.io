---
title: python flask全局变量访问
date: 2018-11-12 16:43:17
tags:
- python
- flask
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

基本使用参考：https://blog.csdn.net/xtingjie/article/details/71210182

遇到的问题记录：在create_app()方法中将一个外部变量赋予全局属性并赋值，然后再另一个蓝图模块中import该变量，返回为None

原因：在注册该新模块的蓝图时，尚未给该变量赋予全局属性，且未赋值，所以为None，实际应在注册蓝图之前赋予

# 代码

```python
test = None
 
 
def create_app(conf_file):
    """
    :param conf_file:
    :return:
    """
    app = Flask(__name__)
    app.config.from_object(ProductionConfig)
    bootstrap.init_app(app)
    db.init_app(app)
 
    scheduler.init_app(app)
    scheduler.start()
    login_manager.init_app(app)
 
    app.url_map.redirect_defaults = False
 
    from .mig import migde as migde_blueprint
    app.register_blueprint(migde_blueprint)
 
    global test  # 改行和下一行提前到注册蓝图前即可
    test = 12
 
    return app
```

```python
from dashboard import test
 
 
@migration.route('/xxx', methods=['GET', 'POST'])
@login_required
def xxxxxx():
    print(test)
```
