---
title: java使用Freemarker模板发送固定格式邮件
date: 2017-10-11 14:18:34
tags:
- java
- freemarker
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 添加maven依赖
```xml
	<dependency>
		<groupId>org.freemarker</groupId>
		<artifactId>freemarker</artifactId>
		<version>2.3.23</version>
	</dependency>
```

# 配置freemarker的bean
```xml
	<!--提供邮件模板支持-->
	<bean id="freeMarkerConfiguration" class="org.springframework.ui.freemarker.FreeMarkerConfigurationFactoryBean">
		<property name="templateLoaderPath" value="classpath:template" />
		<property name="defaultEncoding" value="utf-8" />
	</bean>
```

# 具体代码实现请参考github上的demo
git@github.com:SincerelyUnique/java-mail-with-freemarker.git

# 注意在EmailUtil类中设置好你的发送邮件地址以及SMTP主机，用户名和密码
