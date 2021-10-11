---
title: findbugs异常（2）- Reliance on default encoding
date: 2018-01-02 14:21:14
tags:
- findbugs
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 问题代码

```java
            response.setHeader("Content-Disposition", "attachment;filename="
                    + new String((fileName + ".xlsx").getBytes(), "iso-8859-1"));
```

# 官方说明

```
Reliance on default encoding

Found a call to a method which will perform a byte to String (or String to byte) conversion, 
and will assume that the default platform encoding is suitable. 
This will cause the application behaviour to vary between platforms. 
Use an alternative API and specify a charset name or Charset object explicitly.
```

# 说明

String.getBytes()依赖于系统编码，虽然方便，但是一旦使用就变成了一个技术债务，因为系统的默认编码是不可预知的.

如果要避免这个错误，需要将编码指定好，即：

```java
String.getBytes("GBK")
```

这是getBytes的一个重载方法，可以指定getBytes使用的编码.

