---
title: sourcetree卡顿的一种解决方式
date: 2020-10-10 16:45:36
tags:
- git
- sourcetree
categories:
---

环境：windows10

版本：sourcetree 2.6.10.0

原因：source每次打开会读取大（量）文件，并且每次修改会对这些文件做文本比对

解决：对于不需要读取（或不需要提交、或提交后不再修改）的文件设置忽略

方法一：项目里添加.gitignore并忽略一些文件，[参考](https://git-scm.com/docs/gitignore)，[参考](https://github.com/github/gitignore)

方法二：编辑文件，路径SourceTree -> Tools -> Options -> Git -> Global Ignore List ，[参考](https://www.jianshu.com/p/86f3292d7708)

