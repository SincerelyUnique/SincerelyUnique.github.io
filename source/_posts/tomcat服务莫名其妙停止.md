---
title: tomcat服务莫名其妙停止
date: 2017-11-10 10:03:11
tags:
- tomcat
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

这里只讲述遇到的一种原因：

# 服务停止

最初以为是jvm导致的，但是在/var/log并没有找到jvm致命错误日志（hs_err_pid.log）

又排除了GC情况，使用 jstat -gc pid 5000查看也没发现问题

最后还是从日志入手

原因：日志中存在不明进程销毁日志，如下：

```
Nov 09, 2017 5:30:29 PM org.apache.coyote.AbstractProtocol pause
INFO: Pausing ProtocolHandler ["http-bio-8080"]
Nov 09, 2017 5:30:29 PM org.apache.coyote.AbstractProtocol pause
INFO: Pausing ProtocolHandler ["ajp-bio-8009"]
Nov 09, 2017 5:30:29 PM org.apache.catalina.core.StandardService stopInternal
INFO: Stopping service Catalina
Nov 09, 2017 5:30:29 PM org.apache.coyote.AbstractProtocol stop
INFO: Stopping ProtocolHandler ["http-bio-8080"]
Nov 09, 2017 5:30:29 PM org.apache.coyote.AbstractProtocol stop
INFO: Stopping ProtocolHandler ["ajp-bio-8009"]
Nov 09, 2017 5:30:29 PM org.apache.coyote.AbstractProtocol destroy
INFO: Destroying ProtocolHandler ["http-bio-8080"]
Nov 09, 2017 5:30:29 PM org.apache.coyote.AbstractProtocol destroy
INFO: Destroying ProtocolHandler ["ajp-bio-8009"]
```

而正常使用shutdown.sh关闭时，会在pause日志前存在以下日志：

```
Nov 10, 2017 9:25:08 AM org.apache.catalina.core.StandardServer await
INFO: A valid shutdown command was received via the shutdown port. Stopping the Server instance.
Nov 10, 2017 9:25:08 AM org.apache.coyote.AbstractProtocol pause
INFO: Pausing ProtocolHandler ["http-nio-9080"]
Nov 10, 2017 9:25:08 AM org.apache.coyote.AbstractProtocol pause
INFO: Pausing ProtocolHandler ["ajp-bio-9009"]
```

可见tomcat不是通过正常脚本关闭

还有一种情况是执行kill -9 pid，但是测试了一下，这种情况并没有产生日志

最后怀疑与ssh会话有关，因为我用的是windows+xshell，下班时直接关闭xshell，而恰巧此时tomcat也随之停止了服务。可能是用户直接关闭了ssh会话导致了java进程的强制退出。

下面针对ssh可能引发的问题做了测试：

先关闭服务器

1. 使用命令：sh bin/startup.sh 启动,此时强制关闭xshell，再登录xshell,使用grep查看运行的tomcat，发现tomcat依然运行。
2. 使用命令：sh bin/startup.sh & tail -f logs/catalina.out 启动并查看日志,此时强制关闭xshell，再登录xshell,使用grep查看运行的tomcat，发现tomcat已关闭。
