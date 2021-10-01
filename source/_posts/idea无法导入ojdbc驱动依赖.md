---
title: idea无法导入ojdbc驱动依赖
date: 2019-11-22 16:15:51
tags:
- idea
- ojdbc
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

原因：oracle由于licence的问题限制导入

解决办法：去maven中央仓库找个ojdbc驱动，将jar包下载下来，执行下面命令导入

> C:\Users\jalchu\IdeaProjects\xxl-job-oracle>mvn install:install-file -DgroupId=com.oracle -DartifactId=ojdbc8 -Dversion=12.2.0.1 -Dpackaging=jar -Dfile=C:\Users\jalchu\Desktop\xxl_job\ojdbc8-12.2.0.1.jar -DgeneratePom=true
