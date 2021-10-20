---
title: jsp使用sessionScope获取session值
date: 2017-08-14 11:33:52
tags:
- jsp
- session
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

场景：有些实体对象可以放到HttpSession对象中，保正在一个会话期间可以随时获取这个对象的属性，例如可以将登录用户的信息写入session，以保证页面随时可以获取并显示这个用户的状态信息。下面以此为例。

1. 后台将用户这一实体对象写入session（HttpSession），这里是从拦截器中写入，代码中的userInfo是从单点登录服务器获取的登录用户信息

```java
User user = new User(userInfo);
session.setAttribute("loginUser", user);
```

2. 前台使用c标签set属性值
   参考： http://www.yiibai.com/jsp/jstl_core_set_tag.html
   ```jsp
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
    <c:set var="user" value="${sessionScope.loginUser}"/>
   ```

3. 使用，name是userInfo对象里的属性

```
${user.name}
```


