---
title: Linux基本操作命令
date: 2017-08-07 21:17:20
tags:
- linux
categories:
- [学习, DevOPS, Linux]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 查找文件内容

从某个具体路径查询：`grep -F "ccfly" -R /root/zy/hahah`

从当前路径查询：`grep -F "查询内容" -R ./`

查询tomcat下18年8月15日10点01到10点02之间的日志：`grep '2018-08-15 10:0[1-2]' logs/catalina.out`

# ssh（带端口，不带去掉即可）

`ssh root@106.15.61.237 -p 10333`

# sftp（带端口，不带去掉即可）

`sftp -oPort=10333 root@106.15.61.237`

# 后台部署jar包,关闭ssh不停止服务

`nohup java -jar jar包 &`

# 查看占用端口的进程

`netstat -ntulp |grep 8080`

# 查看tomcat日志

`tail -f logs/catalina.out`

# 创建文件夹

`mkdir package`

# 删除

`rm rmpackage`

# 递归删除文件夹及其下所有文件和文件夹

`rm -rf rmpackage`

# 编辑文件

`vi server.xml`

# 查找某个文件的路径

全局查找：`find / -name xxx.jsp`

从当前路径下查找：`find ./ -name xxx.jsp`

