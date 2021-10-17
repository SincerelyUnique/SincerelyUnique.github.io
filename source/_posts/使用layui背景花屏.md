---
title: 使用layui背景花屏
date: 2017-10-11 11:21:23
tags:
- layui
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

工作中使用layui遇到一个问题：点击按钮，弹出一个div，当移动div时，背景花屏，如下

![](../images/layui01.png)

原因：当前使用的layui版本小bug


解决：添加css样式

```
.layui-layer-shade{
    opacity:1!important;
    background-color:rgba(0,0,0,.8)!important;
}
```
