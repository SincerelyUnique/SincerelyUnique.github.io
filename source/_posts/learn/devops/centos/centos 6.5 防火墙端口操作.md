---
title: centos 6.5 防火墙端口操作
date: 2017-08-07 21:25:06
tags:
- centos
categories:
- [学习, DevOPS, CentOS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

场景：开发完毕，测试时遇到一个问题，公司申请的一个空间安装的是centos 6.5版本，默认防火墙未打开，但是性能测试那边因为安装某个插件打开了防火墙（是安装nvc，具体为什么打开不太清楚），由于防火墙打开了，此时我之前部署到这个系统的tomcat服务器遇到访问超时问题，所以需要手动将8080端口添加到防火墙允许放行的列表中。

1.临时关闭防火墙

`# service iptables stop`



2.手动增加防火墙端口

`#/sbin/iptables -I INPUT -p tcp --dport 9080 -j ACCEPT`

`#/sbin/iptables -I INPUT -p tcp --dport 8080 -j ACCEPT`

然后保存：

`#/etc/rc.d/init.d/iptables save`

3.查看防火墙状态

`# /etc/init.d/iptables status`

4.重启防火墙以便改动生效:(或者直接重启系统)

`# /etc/init.d/iptables restart`
