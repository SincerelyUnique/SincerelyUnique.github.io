---
title: java swing运行main方法时遇到NPE
date: 2018-03-13 19:33:05
tags:
- java
- swing
categories:
- [学习, Java语言学习, Java]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> Exception in thread &quot;AWT-EventQueue-0&quot; java.lang.NullPointerException

```java
SwingUtilities.invokeLater(new   Runnable(){ 
            public   void   run() { 
            //放入方法
            } 
 });
```

java swing运行main方法时遇到NPE，如上解决



参考；

http://blog.csdn.net/t1dmzks/article/details/64500888
