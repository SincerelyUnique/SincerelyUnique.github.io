---
title: innerText()获取DOM值后面带空格问题
date: 2018-11-07 16:29:39
tags:
- html
- js
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

![inner01](../images/inner01.png)

eq返回的是JQuery对象，下标返回的是原始的DOM对象，具体原因要看怎么实现的了

但是在chrome67版本这两者获取的值是相同，而chrome70就不同了，不清楚是不是低版本的bug）
