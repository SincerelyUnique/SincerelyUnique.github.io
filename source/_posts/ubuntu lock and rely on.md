---
title: ubuntu lock and rely on
date: 2015-10-27 08:51:40
tags:
- ubuntu
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

提示：“E: 无法获得锁 /var/lib/dpkg/lock - open (11: 资源暂时不可用)

E: 无法锁定管理目录(/var/lib/dpkg/)，是否有其他进程正占用它？”


1. 终端输入 ps -aux ，列出进程。找到含有apt‘-get的进程，直接sudo kill PID（含有apt-get进程的PID）。

2. 强制解锁,命令:

   sudo rm /var/cache/apt/archives/lock

   sudo rm /var/lib/dpkg/lock

3.$ sudo apt-get update

4.$ sudo apt-get upgrade

5.$ sudo apt-get -f install

