---
title: python禁止字典key排序
date: 2019-07-17 11:26:25
tags:
- python
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 正常的python dict是按字母顺序排序的，所以要使用OrderedDict来自定义顺序

```python
        import collections
        data = collections.OrderedDict()
        data['b'] = 3
        data['a'] = 1
        data = jsonify(d)
        return make_response(data, 200)
```

2. flask框架的jsonify默认也是按字母顺序排序的，所以在flask项目启动时添加一个启动参数

```python
        app.config["JSON_SORT_KEYS"] = False
```

官方文档解释如下：

> Sort the keys of JSON objects alphabetically. This is useful for caching because it ensures the data is serialized the same way no matter what Python’s hash seed is. While not recommended, you can disable this for a possible performance improvement at the cost of caching.

### 参考

https://github.com/pallets/flask/issues/974

https://flask.palletsprojects.com/en/2.0.x/config/#JSON_SORT_KEYS
