---
title: java.lang.IllegalStateException Neither BindingResult nor plain target object for bean name 'user'
date: 2017-08-07 15:24:22
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> 场景：在《spring+mybatis企业应用实战》一书中第五章，当使用spring mvc国际化时，因为在jsp中没有引入一个实体类（在Page 90页有个loginForm.jsp代码），导致运行时抛出异常：java.lang.IllegalStateException: Neither BindingResult nor plain target object for bean name 'user' available as request attribute，具体异常如下：

```
严重: Neither BindingResult nor plain target object for bean name 'user' available as request attribute
java.lang.IllegalStateException: Neither BindingResult nor plain target object for bean name 'user' available as request attribute
	at org.springframework.web.servlet.support.BindStatus.<init>(BindStatus.java:144)
	at org.springframework.web.servlet.tags.form.AbstractDataBoundFormElementTag.getBindStatus(AbstractDataBoundFormElementTag.java:168)
	at org.springframework.web.servlet.tags.form.AbstractDataBoundFormElementTag.getPropertyPath(AbstractDataBoundFormElementTag.java:188)
	at org.springframework.web.servlet.tags.form.AbstractDataBoundFormElementTag.getName(AbstractDataBoundFormElementTag.java:154)
	at org.springframework.web.servlet.tags.form.AbstractDataBoundFormElementTag.autogenerateId(AbstractDataBoundFormElementTag.java:141)
	at org.springframework.web.servlet.tags.form.AbstractDataBoundFormElementTag.resolveId(AbstractDataBoundFormElementTag.java:132)
	at org.springframework.web.servlet.tags.form.AbstractDataBoundFormElementTag.writeDefaultAttributes(AbstractDataBoundFormElementTag.java:116)
	at org.springframework.web.servlet.tags.form.AbstractHtmlElementTag.writeDefaultAttributes(AbstractHtmlElementTag.java:422)
	at org.springframework.web.servlet.tags.form.InputTag.writeTagContent(InputTag.java:142)
	at org.springframework.web.servlet.tags.form.AbstractFormTag.doStartTagInternal(AbstractFormTag.java:84)
	at org.springframework.web.servlet.tags.RequestContextAwareTag.doStartTag(RequestContextAwareTag.java:80)
	at org.apache.jsp.WEB_002dINF.views.home_jsp._jspx_meth_form_005finput_005f0(home_jsp.java:245)
	at org.apache.jsp.WEB_002dINF.views.home_jsp._jspx_meth_form_005fform_005f0(home_jsp.java:166)
	at org.apache.jsp.WEB_002dINF.views.home_jsp._jspService(home_jsp.java:87)
	at org.apache.jasper.runtime.HttpJspBase.service(HttpJspBase.java:70)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:727)
	at org.apache.jasper.servlet.JspServletWrapper.service(JspServletWrapper.java:432)
	at org.apache.jasper.servlet.JspServlet.serviceJspFile(JspServlet.java:395)
	at org.apache.jasper.servlet.JspServlet.service(JspServlet.java:339)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:727)
```

出问题的页面代码：

```jsp
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<html>
<head>
    <meta http-equiv="content-type" content="text/html;charset=UTF-8">
    <title>测试基于浏览器请求的国际化</title>
</head>
<body>
 
<h2><spring:message code="title"/></h2>
<form:form modelAttribute="user" method="post" action="login">
    <table>
        <tr>
            <td><spring:message code="loginname"/></td>
            <td><form:input path="loginname"/></td>
        </tr>
        <tr>
            <td><spring:message code="password"/></td>
            <td><form:input path="password"/></td>
        </tr>
        <tr>
            <td><input type="submit" value="<spring:message code="submit"/>"></td>
        </tr>
    </table>
</form:form>
 
</body>
</html>
```

原因：觉得应该是 modelAttribute="user" 这句问题，可以查看标签说明（在spring-form.tld）中这么描述：

```xml
    <attribute>
        <description>Name of the model attribute under which the form object is exposed.
        Defaults to 'command'.</description>
        <name>modelAttribute</name>
        <required>false</required>
        <rtexprvalue>true</rtexprvalue>
    </attribute>
```

（可以参考这篇博客： http://blog.csdn.net/fclwd/article/details/9401531）

解决：在jsp中添加：

```
<jsp:useBean id="user" class="com.demo.domain.User" scope="request"/>
```

