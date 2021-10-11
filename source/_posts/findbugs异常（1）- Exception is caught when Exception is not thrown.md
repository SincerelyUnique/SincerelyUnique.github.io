---
title: findbugs异常（1）- Exception is caught when Exception is not thrown
date: 2018-01-02 14:05:54
tags:
- findbugs
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# findbugs官方解释

```
Exception is caught when Exception is not thrown
This method uses a try-catch block that catches Exception objects, 
but Exception is not thrown within the try block, 
and RuntimeException is not explicitly caught. 
It is a common bug pattern to say try { ... } catch (Exception e) { something } as a shorthand for catching a number of types of exception each of whose catch blocks is identical, 
but this construct also accidentally catches RuntimeException as well, 
masking potential bugs.
A better approach is to either explicitly catch the specific exceptions that are thrown, or to explicitly catch RuntimeException exception, rethrow it, and then catch all non-Runtime Exceptions, as shown below:
  try {
    ...
  } catch (RuntimeException e) {
    throw e;
  } catch (Exception e) {
    ... deal with all non-runtime exceptions ...
  }
```

# 一般人都会这样写代码

```java
  try{
　　　　//
　　}
　　catch(Exception ex){
　　　　//
　　}
```

# 推荐写法

上面这样很省事，但是JAVA规范中并不推荐这样做，这样是属于“过泛地捕获异常”，因为try{}中可能出现的异常种类有很多，上面的做法不利于分别处理各种异常，建议根据业务需求，分别捕获需要特别处理的异常，例子如下：

```java
  try{
　　　　//
　　}
　　catch(SQLException ex){
　　　　//
　　}
　　catch(IOException ex){
　　　　//
　　}
　　catch(Exception ex){
　　　　//
　　}
```

捕获的异常一定要抛出，并打印日志。
