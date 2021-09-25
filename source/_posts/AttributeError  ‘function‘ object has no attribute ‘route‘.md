---
title: AttributeError ‘function‘ object has no attribute ‘route‘
date: 2021-05-22 23:46:56
tags:
- python
categories:
---

1.比较隐蔽的一个问题，问题代码如下 

```python
from app.settings import settings
 
# the name settings refers to the blueprint imported above
@settings.route('/a')
def this_works():
    ...
 
# the name settings refers to the blueprint imported above
@settings.route('/')
def settings():
    ...
 
# the name settings now refers to the function defined above
@settings.route('/b')
def this_fails():
    ...
```

2.造成原因是函数名和路由名一样了，解决办法给路由起别名或者改变上面第二个方法的函数名字（不要叫settings - 即不要和blueprint的settings一样）

```python
from app.settings import settings as bp
 
@bp.route('/')
def settings():
    pass
```

参考：https://stackoverflow.com/questions/36798380/registering-route-on-blueprint-raises-attributeerror-function-object-has-no-a
