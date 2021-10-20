---
title: 实体间属性copy使用@JsonIgnoreProperties忽略部分属性
date: 2017-08-09 09:42:47
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> 场景：当我们访问远程服务器，得到返回的json数据中封装的实体属性可能有些是不必要的，当我们在自己的项目中可以创建一个vo去接收这个实体属性，这个vo里属性可能只包含原实体属性的一部分，此时如果不做处理，会抛出数据接收字段缺失的异常，此时需要使用@JsonIgnoreProperties忽略部分属性，下面以获取用户数据为例，在我们项目中只需要获取用户实体中的employeeCode和employeePosition这两个字段。

1. 访问远程服务器得到的实体：RemoteUserInfo

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class RemoteUserInfo {
 
    private String employeeCode;
 
    private String employeePosition;
 
    private String age;
 
    private String sex;
 
}
```

2. 定义本地项目中的接收对象：LocalUserInfoVo

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
//加入下面注解
@JsonIgnoreProperties(ignoreUnknown = true)
public class LocalUserInfoVo {
 
    private String employeeCode;
 
    private String employeePosition;
 
}
```

