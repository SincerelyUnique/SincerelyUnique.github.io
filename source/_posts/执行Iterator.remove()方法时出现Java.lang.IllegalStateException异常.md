---
title: 执行Iterator.remove()方法时出现Java.lang.IllegalStateException异常
date: 2017-09-04 10:25:47
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

原因：删除了一个不满足条件的元素

1. 首先注意迭代时要next一下

```java
it.next();
```

2. 注意remove对象是否存在，如果这个记录已被remove掉，再次remove会出现此异常，容易出现在对同一对象（如List）做多次迭代remove的情景中
