---
title: getResponseBodyAsString()乱码
date: 2018-04-24 11:27:36
tags:
- codec
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```java
        GetMethod get = new GetMethod("/xxx/user");
        get.addRequestHeader("accept", "application/xml");  // 视具体情况而定，可以从浏览器或postman查看下请求的header
        get.getParams().setParameter(HttpMethodParams.HTTP_CONTENT_CHARSET,"utf-8"); //这里设置具体编码，视具体接口而定
```

参考： https://www.cnblogs.com/yixiu868/p/6637645.html
