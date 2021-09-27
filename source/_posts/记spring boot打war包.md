---
title: 记spring boot打war包
date: 2020-07-09 14:14:35
tags:
- spring boot
- java
categories:
---

## pom package改为war

```xml
<packaging>war</packaging>
```

## 排除tomcat依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## 添加servlet依赖

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
```

## 添加war插件

```xml
<build>
    <plugins>
        <plugin>
            <artifactId>maven-war-plugin</artifactId>
            <version>2.6</version>
            <configuration>
                <!--如果想在没有web.xml文件的情况下构建WAR，请设置为false。-->
                <failOnMissingWebXml>false</failOnMissingWebXml>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## 修改spring boot启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;
import org.springframework.cache.annotation.EnableCaching;
 
 
@SpringBootApplication
@EnableCaching
public class DemoApplication extends SpringBootServletInitializer {
 
	public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
	}
 
	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
		return application.sources(DemoApplication.class);
	}
}
```

## 打包

> mvn clean package
