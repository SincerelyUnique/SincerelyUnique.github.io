---
title: findbugs异常（4）- Call to static DateFormat
date: 2018-01-02 15:13:25
tags:
- findbugs
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 问题代码

```java
private static final DateFormat df = new SimpleDateFormat("yyyy年MM月");
 
public String convertDate( Date date ){
  String date = df.format(date);
  return date;
}
```

# 官方解释

```
Call to static DateFormat

As the JavaDoc states, DateFormats are inherently unsafe for multithreaded use. The detector has found a call to an instance of DateFormat that has been obtained via a static field. This looks suspicous.
For more information on this see Sun Bug #6231579 and Sun Bug #6178997.
```

[Sun Bug #6231579](https://bugs.java.com/bugdatabase/view_bug.do?bug_id=6231579)

[Sun Bug #6178997](https://bugs.java.com/bugdatabase/view_bug.do?bug_id=6178997)

DateFormat不是线程安全的
