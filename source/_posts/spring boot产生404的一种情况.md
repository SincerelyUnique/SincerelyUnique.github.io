---
title: spring boot产生404的一种情况
date: 2018-02-12 10:25:37
tags:
- springboot
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

问题：简单地写了一个get请求，返回一个字符串，页面出现404



原因：依赖添加错误



错误的依赖配置：
```xml
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
		</dependency>
```

正确的依赖配置：
```xml
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
```

