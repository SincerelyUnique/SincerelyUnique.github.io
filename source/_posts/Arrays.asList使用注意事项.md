---
title: Arrays.asList使用注意事项
date: 2017-10-12 17:12:24
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

使用Arrays.asList操作，从根本上讲还是操作原来的数据array

```java
public static void main(String[] args){
        String[] strArr = new String[]{"a","b","c","d"};
        List<String> list = Arrays.asList(strArr);
        //list.add("e");                               //抛出java.lang.UnsupportedOperationException
        System.out.println(strArr[0]);                 //输出‘a’
        strArr[0] = "e";                               //list.get(0)会变，其实还是array
        System.out.println(strArr[0]);                 //输出‘e’
}
```
