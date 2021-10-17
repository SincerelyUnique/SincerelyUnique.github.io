---
title: 获取cookie里内容
date: 2017-09-27 08:35:30
tags:
- cookie
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```java
public String[] getUsernameAndTokenFromCookies(HttpServletRequest request){
    String[] usernameAndToken = new String[2];
 
    Cookie[] cookies = request.getCookies();
    String usernameInCookie = null;
    String tokenInCookie = null;
 
    if( cookies != null ){
        for (int i = 0; i < cookies.length; i++){
            Cookie cookie = cookies[i];
            if (cookie.getName().equals("username")){
                usernameInCookie = cookie.getValue();
            }
            if (cookie.getName().equals("token")){
                tokenInCookie = cookie.getValue();
            }
        }
    }
 
    usernameAndToken[0] = usernameInCookie;
    usernameAndToken[1] = tokenInCookie;
 
    return usernameAndToken;
}
```
