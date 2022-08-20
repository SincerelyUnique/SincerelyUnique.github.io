---
title: tomcat8.5进控制台403 Access Denied
date: 2018-02-02 08:44:38
tags:
- tomcat
categories:
- [学习, DevOPS, Tomcat]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

修改文件webapps/manager/META-INF/context.xml

找到下面属性标签 Value

注释掉

```xml
<Context antiResourceLocking="false" privileged="true" >
 <!--
 <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
  -->
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
```

参考地址：[stackoverflow](https://stackoverflow.com/questions/38551166/403-access-denied-on-tomcat-8-manager-app-without-prompting-for-user-password)
