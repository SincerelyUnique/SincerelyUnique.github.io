---
title: form表单中select组件使用for循环赋值
date: 2017-08-07 20:56:17
tags:
- html
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> 使用场景：当我们的select下拉菜单中可选项过多，可以通过for循环来渲染，使用如下。

注意在js代码中嵌入了java代码

```
<select id="selectEndMinutes" name="quiz1">
<option value="">请选择分钟</option>
<%
for(int i=1;i<=60;i++){
%>
<option value="<%=i %>"><%=i %></option>
<%} %>
</select>
```

