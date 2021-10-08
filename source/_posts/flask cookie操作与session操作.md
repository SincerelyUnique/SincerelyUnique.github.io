---
title: flask cookie操作与session操作
date: 2018-09-27 14:48:26
tags:
- flask
- cookie
- session
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
from flask import Flask
from flask import render_template
from flask import request
from flask import make_response
from datetime import datetime, timedelta
 
 
@app.route('/set_cookie')
def set_cookie():
    # 设置cookie
    response = make_response('Hello World')
    out_date = datetime.today() + timedelta(days=30)
    response.set_cookie('Name', 'Hyman', expires=out_date)
    return response
 
 
@app.route('/get_cookie')
def get_cookie():
    # 获取cookie
    name = request.cookies.get('Name')
    print(name)
    return render_template('cookie.html')
 
 
@app.route('/del_cookie')
def del_cookie():
    # 清除cookie1
    response = make_response('delete cookie')
    response.set_cookie('Name', '', expires=0)
    return response
 
 
@app.route('/del_cookie2')
def del_cookie2():
    # 清除cookie2
    response = make_response('delete cookie2')
    response.delete_cookie('Name')
    return response
```

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>页面获取cookie</title>
</head>
<body>
    <h1>My name is {{request.cookies.get('Name')}}</h1>
</body>
</html>
```

转自：https://blog.csdn.net/guoqianqian5812/article/details/75305697

```python
import os
from flask import Flask
from flask import session
from datetime import timedelta
 
app = Flask(__name__)
 
 
if __name__ == '__main__':
    app.run(debug=True)
 
 
app.config['SECRET_KEY'] = os.urandom(24)   # 设置为24位的字符,每次运行服务器都是不同的，所以服务器启动一次上次的session就清除。
app.config['PERMANENT_SESSION_LIFETIME'] = timedelta(days=7)  # 设置session的保存时间。
 
 
@app.route('/')
def index():
    session.permanent = True
    session['username'] = 'xxx'
    return 'Hello World!'
 
 
@app.route('/get/')
def get():
    return session.get('username')
 
 
@app.route('/delete/')
def delete():
    print(session.get('username'))
    session.pop('username')
    print(session.get('username'))
    return 'delete'
 
 
@app.route('/clear/')
def clear():
    print(session.get('username'))
    session.clear()
    print(session.get('username'))
    return 'clear'
```

转自：https://www.cnblogs.com/nimingdaoyou/p/9037655.html
