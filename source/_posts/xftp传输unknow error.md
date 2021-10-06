---
title: xftp传输unknow error
date: 2019-02-26 14:51:59
tags:
- xftp
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

原因：磁盘满了

解决：使用df /home查看改目录使用情况，use达到100%，然后使用du -sh逐级判断文件夹，最后定位到log文件，删掉log文件并kill掉更新该log的进程
