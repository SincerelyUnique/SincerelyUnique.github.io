---
title: 新项目部署遇到的一些问题
date: 2022-12-06 14:34:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 背景

最近参与的一个新的需求，遇到一些问题，简单记录一下。

简要说明，新需求需要整合两个service：
- 一个是springboot + bootstrap vue，我们并不是该项目owner，仅做一些业务植入
- 一个是python flask + vue element，这是我们自己的主要项目之一，处理实际业务

前端的一部分从vue element移植到bootstrap vue，并在bootstrap vue端增加部分新页面

后端以springboot做proxy，请求转发到python，具体业务实现在python端

# 问题

1. idea download source code Connection refused to host: 127.0.0.1; nested exception is

- 参考： https://stackoverflow.com/questions/68348626/fix-intellij-idea-download-sources-connection-refused-to-host-127-0
-0-1-error
- 解决： 修改JDK for importer


2. Get request body from HttpServletRequest

- 参考： https://stackoverflow.com/questions/8100634/get-the-post-request-body-from-httpservletrequest
- 解决： String test = request.getReader().lines().collect(Collectors.joining(System.lineSeparator()));
- 其他： getInputStream()


3. @Value注入属性与final修饰问题

- 参考： https://stackoverflow.com/questions/65797536/why-java-final-variables-cannot-be-assigned-a-value-in-the-setter
-method
- 原因： 属性注入时机
- 解决： 
  ```java
  private final String HOST;
  public XxxServiceImpl(@Value("${api.url.xxx.endpoint.host}") String url){
      this.HOST = url;
  }
  ```


4. 为什么无法从HttpServletRequest读取多次body

- 参考： https://stackoverflow.com/questions/71997116/why-can-the-body-of-the-httpservletrequest-not-be-read-multiple-times
- 原因： 性能，内存方便的优化
- 办法： 自定义RequestWrapper类继承HttpServletRequestWrapper，作为请求拦截器，在该类定义成员变量cache body属性，或者继承OncePerRequestFilter重写doFilterInternal方法等等


5. tomcat首页访问Manager App报403

- 原因： 因为安全因素，tomcat默认限制非本机用户访问
- 解决： 修改apache-tomcat-8.5.84/webapps/manager/META-INF/context.xml，注释掉127那个标签


6. tomcat修改端口，多tomcat共存场景

- 解决： 修改apache-tomcat-8.5.84/conf/server.xml，8005/8080/8009，统一+1


