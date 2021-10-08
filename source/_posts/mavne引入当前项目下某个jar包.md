---
title: mavne引入当前项目下某个jar包
date: 2018-03-13 18:36:59
tags:
- jar
- maven
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```xml
    <dependency>
      <groupId>com.hope.cloud</groupId>  <!--自定义-->
      <artifactId>cloud</artifactId>    <!--自定义-->
      <version>1.0</version> <!--自定义-->
      <scope>system</scope> <!--system，类似provided，需要显式提供依赖的jar以后，Maven就不会在Repository中查找它-->
      <systemPath>${basedir}/src/main/webapp/lib/beautyeye_lnf.jar</systemPath> <!--项目根目录下的lib文件夹下-->
    </dependency>
```

路径：

![beauty01](../images/beauty01.png)
