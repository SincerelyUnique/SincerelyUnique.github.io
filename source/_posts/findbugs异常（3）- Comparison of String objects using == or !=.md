---
title: findbugs异常（3）- Comparison of String objects using == or !=
date: 2018-01-02 14:53:33
tags:
- findbugs
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 问题代码

```java
if (dutyOfficerNameAccount != "" && dutyOfficerNameAccount != null) {
	//
}
```

# 官方解释

```
Comparison of String objects using == or !=

This code compares java.lang.String objects for reference equality using the == or != operators. 
Unless both strings are either constants in a source file, or have been interned using the String.intern() method, 
the same string value may be represented by two different String objects. 
Consider using the equals(Object) method instead.
```

从字面意思可以理解String对象进行比较的时候：只有两种情况可以使用== or !=的，这两种情况是；在源文件中是个常数或者是调用
String.intern()方法，使用String的规范化表示形式来进行比较,如果不是这两中情况的话推荐使用.equals(object)方式。
