---
title: maven中groupid和artifactid意思
date: 2017-09-04 11:02:42
tags:
- maven
categories:
- [学习, Java语言学习, Maven]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 此解释来自百度百科

GroupID是项目组织唯一的标识符，实际对应JAVA的包的结构，是main目录里java的目录结构。

ArtifactID就是项目的唯一的标识符，实际对应项目的名称，就是项目根目录的名称。
一般GroupID就是填com.leafive.test这样子。

2. 具体可以查看spring官网对pom文件标签的解释：http://spring.io/guides/gs/maven/

① 这个是pom文件头的

```
<modelVersion>. POM model version (always 4.0.0).
<groupId>. Group or organization that the project belongs to. Often expressed as an inverted domain name.
<artifactId>. Name to be given to the project’s library artifact (for example, the name of its JAR or WAR file).
<version>. Version of the project that is being built.
<packaging> - How the project should be packaged. Defaults to "jar" for JAR file packaging. Use "war" for WAR file packaging.
```

② 这个是dependency中的

```
<groupId> - The group or organization that the dependency belongs to.
<artifactId> - The library that is required.
<version> - The specific version of the library that is required.
```
