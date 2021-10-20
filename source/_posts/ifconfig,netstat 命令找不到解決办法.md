---
title: ifconfig,netstat 命令找不到解決办法
date: 2017-08-07 21:28:53
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
- [root@thunderspeed ~]# ifconfig
- bash: ifconfig: command not found...
- [root@thunderspeed ~]#
- [root@thunderspeed ~]# netstat
- bash: netstat: command not found...
- [root@thunderspeed ~]#
```

```
- sudo yum provides ifconfig
- sudo yum install net-tools
```
