---
title: 使用xml模板导出excel时遇到尖括号转义的问题解决
date: 2017-11-20 17:12:59
tags:
- java
- xml
- excel
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 问题描述

java freemarker使用xml作为模板，导出带有'<>'（尖括号）的字段属性值，遇到了转译的问题。

# 问题解决

使用第三方jar包，apache common lang3 

```java
StringEscapeUtils.escapeXml( '橘子<orange>,' )
```
