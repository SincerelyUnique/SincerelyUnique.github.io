---
title: bootstrap datetimepicker组件当日日期点击没反应、格式化format和值的获取
date: 2018-11-08 09:55:17
tags:
- bootstrap 
categories:
- [学习, 前端知识学习, Bootstrap]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

（1）当日日期点击没反应，但是点击当日的昨天可以，解决办法，在当日基础上叠加一天并禁用明天，使之仅能选择今天或之前日期

（2）format格式化问题，年月日全小写不通过，要全大写，尝试了很多最后参考：https://jsfiddle.net/n9aofvht/7/

（3）值的获取，简单通过jquery的id选择器去获取value为空，参考了：https://stackoverflow.com/questions/16573624/get-the-value-of-bootstrap-datetimepicker-in-javascript

代码:

```javascript
    $('#startDatePicker,#endDatePicker').datetimepicker({
        useCurrent: false,
        maxDate: moment().add(1, 'days'),
        minDate: moment().subtract(7, 'days'),
        format: 'YYYY-MM-DD',
        disabledDates: [
            moment( ((new Date()).getMonth() + 1) + '/' + ((new Date()).getDate() + 1) + '/' +  (new Date()).getFullYear() )
        ]
    });
```

取值:

```javascript
$("#endDatePicker").find("input").val();
```
