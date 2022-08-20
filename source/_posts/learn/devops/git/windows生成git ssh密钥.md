---
title: windows生成git ssh密钥
date: 2017-08-04 16:30:49
tags:
- git
categories:
- [学习, DevOPS, Git]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 安装git，首先去git官网下载git，https://git-scm.com/downloads，下载.exe格式并安装。

![](../../../../images/git01.png)

安装完是这样的

![](../../../../images/git02.png)

2. 安装TortoiseGit，下载地址https://tortoisegit.org/download/，如下

![](../../../../images/git03.png)

记得安装过程中要添加一个git.exe文件，这个文件在上一个安装文件的路径下面：

![](../../../../images/git04.png)

3. 从程序目录启动“Git Bash”

![](../../../../images/git05.png)

4. 键入命令：`ssh-keygen -t rsa -C "email@email.com"`,"email@email.com"是你的github账号

![](../../../../images/git06.png)

5. 此时，你的C:\Users\admin\.ssh这个路径下会生成两个文件：id_rsa和id_rsa.pub

6. 用记事本打开id_rsa.pub文件，复制内容，在github.com的网站上到ssh密钥管理页面，添加新公钥，随便取个名字，内容粘贴刚才复制的内容。

![](../../../../images/git07.png)



