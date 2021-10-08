---
title: flask-pyecharts-jinja2简单使用
date: 2018-10-18 10:23:17
tags:
- flask
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Python代码

```python
@main.route('/report_chart', methods=['GET', 'POST'])
@login_required
def report_chart():
    # define a page
    page = Page()
 
    # define a pie chart
    attr1 = ["New", "Pending", "Verified", "Fix", "Success"]
    value = [11, 12, 13, 10, 10]
    pie = Pie("Pie Chart Demo")
    pie.add("", attr1, value, is_label_show=True, center=[50, 50])
    page.add_chart(pie)
 
    # define a bar chart
    attr2 = ["New", "Pending", "Verified", "Fix", "Success"]
    v1 = [5, 20, 36, 10, 75]
    v2 = [10, 25, 8, 60, 20]
    bar = Bar("Bar Chart Demo")
    bar.add("Shop A", attr2, v1, is_stack=True)
    bar.add("Shop B", attr2, v2, is_stack=True)
    page.add_chart(bar)
 
    attr3 = ["New", "Pending", "Verified", "Fix", "Success"]
    v11 = [5, 20, 36, 10, 10]
    v22 = [55, 60, 16, 20, 15]
    line = Line("Line Chart Demo")
    line.add("Shop A", attr3, v11, mark_point=["average"])
    line.add("Shop B", attr3, v22, is_smooth=True, mark_line=["max", "average"])
    page.add_chart(line)
 
    return render_template('report_chart.html',
                           chart=page.render_embed(),
                           host='http://chfw.github.io/jupyter-echarts/echarts',
                           script_list=page.get_js_dependencies())
```

# Jinja2模板

```html
    {% for jsfile_name in script_list %}
        <script src="{{host}}/{{jsfile_name}}.js"></script>
    {% endfor %}
```

```html
                <div>
                    {{chart|safe}}
                </div>
```

如果想要显示单个图表，将render_template方法里的chart属性的对象由page改为具体的图表对象即可

如改为Line图：

```python
    return render_template('report_chart.html',
                           chart=line.render_embed(),
                           host='http://chfw.github.io/jupyter-echarts/echarts',
                           script_list=line.get_js_dependencies())
```

参考：

pyecharts官方文档：http://pyecharts.org/#/zh-cn/charts_base
