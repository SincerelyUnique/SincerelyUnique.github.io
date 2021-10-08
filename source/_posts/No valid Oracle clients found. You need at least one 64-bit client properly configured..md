---
title: No valid Oracle clients found. You need at least one 64-bit client properly configured.
date: 2018-01-15 13:51:51
tags:
- oracle
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 问题：安装了toad for oracle12.12后，当打开软件时显示该错误。

![](../images/toad01.png)

2. 原因：没有安装64位oracle客户端。

3. 解决办法：

（1）下载安装toad for oracle 12.12 （其他版本也可以）

（2）打开后，你会看到上面的错误提示。

（3）下载 instant client 12.2

（4）解压到任意位置，如C:\app\instantclient_12_2

（5）设置环境变量，“TNS_ADMIN”，

（6）同理，设置系统Path变量

（7）然后把你之前配置好的 tnsnames.ora 文件移动到 C:\app\instantclient_12_2 中，这样就做好了。

附录：

本文参考自[stackexchange](https://dba.stackexchange.com/questions/175333/db-client-tool-to-get-the-error-message-when-open-toad-oracle-client)。
