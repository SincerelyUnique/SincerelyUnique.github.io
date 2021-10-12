---
title: java运算保留两位小数
date: 2017-11-20 17:29:02
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```java
public static void main(String[] args) {
        BigDecimal b = new BigDecimal("20").divide(new BigDecimal("3"), 2, BigDecimal.ROUND_UP);
        System.out.println( b );
    }
```
