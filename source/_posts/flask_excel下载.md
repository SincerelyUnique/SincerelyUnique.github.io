---
title: flask_excel下载
date: 2018-10-22 16:12:18
tags:
- flask
- excel
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 后台代码

```python
@main.route('/report_export', methods=['GET'])
@login_required
def report_export():
    query_sets = Partner.query.limit(10).all()
    column_names = ['partnerid', 'partnername']
    # pip install pyexcel-xlsx && pip install Flask-Excel
    return flask_excel.make_response_from_query_sets(query_sets, column_names, file_type="xlsx", status=200, file_name='test')
```

```python
if __name__ == '__main__':
    flask_excel.init_excel(app)
    manager.run()
```

# 前端

```
<a href="{{ url_for('main.report_export') }}">Export</a>
```

参考文档（里面有导入导出、下载等）：

https://media.readthedocs.org/pdf/flask-excel/latest/flask-excel.pdf
