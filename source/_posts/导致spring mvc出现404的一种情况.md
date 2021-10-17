---
title: 导致spring mvc出现404的一种情况
date: 2017-09-27 10:56:07
tags:
- spring
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

原因：web配置文件引入了两个配置文件，一个是spring的，一个是spring mvc的，spring配置文件下配置了包扫描，而spring mvc配置文件没有配置，在spring mvc上填上就好了

```xml
  <!-- Spring 配置 -->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>
      classpath*:spring/spring-context.xml
    </param-value>
  </context-param>
```

```xml
  <!-- spring mvc 配置 -->
  <servlet>
    <description>spring mvc servlet</description>
    <servlet-name>springMvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <description>spring mvc 配置文件</description>
      <param-name>contextConfigLocation</param-name>
      <param-value>
        classpath*:spring/spring-mvc.xml
      </param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>springMvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
```

spring-context.xml中配置的包扫描

```xml
    <!-- 配置要扫描的包 -->
    <context:component-scan base-package="com.example">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
```

spring-mvc.xml中配置的包扫描

```xml
    <!-- 自动扫描controller包下的所有类，使其认为spring mvc的控制器 -->
    <context:component-scan base-package="com.example">
        <context:include-filter type="annotation"
                                expression="org.springframework.stereotype.Controller" />
        <context:include-filter type="annotation"
                                expression="org.springframework.web.bind.annotation.ControllerAdvice" />
        <context:exclude-filter type="annotation"
                                expression="org.springframework.stereotype.Service" />
    </context:component-scan>
```
