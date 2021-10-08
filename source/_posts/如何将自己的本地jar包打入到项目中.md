---
title: 如何将自己的本地jar包打入到项目中
date: 2018-03-30 12:52:13
tags:
- jar
- maven
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

说明：第三方jar包（或自己写的，即在maven中央仓库不存在的），在新项目中有引用，由于是在本地，所以scope是system，无法打入

以网上的beautyeye为例子

```xml
    <dependency>
      <groupId>com.beautyeye</groupId>  <!--自定义-->
      <artifactId>beautyeye</artifactId>    <!--自定义-->
      <version>1.0</version> <!--自定义-->
      <scope>system</scope><!--system，类似provided，需要显式提供依赖的jar以后，Maven就不会在Repository中查找它-->
      <systemPath>${basedir}/src/main/webapp/lib/beautyeye_lnf.jar</systemPath> <!--项目根目录下的lib文件夹下-->
    </dependency>
```

该依赖现在执行mvn package是没有办法打入的

此时可以参考：https://devcenter.heroku.com/articles/local-maven-dependencies

基本操作：

在pom.xml同级目录创建一个仓库repo

执行命令

```bash
mvn deploy:deploy-file -Durl=file:///path/to/yourproject/repo/ -Dfile=mylib-1.0.jar -DgroupId=com.example -DartifactId=mylib -Dpackaging=jar -Dversion=1.0
```

执行完后，在pom.xml引入该仓库

```xml
<repositories>
    <!--other repositories if any-->
    <repository>
        <id>project.local</id>
        <name>project</name>
        <url>file:${project.basedir}/repo</url>
    </repository>
</repositories>
```

此时依赖修改为

```xml
    <dependency>
      <groupId>com.beautyeye</groupId>  <!--自定义-->
      <artifactId>beautyeye</artifactId>    <!--自定义-->
      <version>1.0</version> <!--自定义-->
      <!--<scope>system</scope>--><!--system，类似provided，需要显式提供依赖的jar以后，Maven就不会在Repository中查找它-->
      <!--<systemPath>${basedir}/src/main/webapp/lib/beautyeye_lnf.jar</systemPath>--> <!--项目根目录下的lib文件夹下-->
    </dependency>
```

再次打包就会把第三方（或称本地）的jar包打进去了
