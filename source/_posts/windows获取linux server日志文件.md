---
title: windows获取linux server日志文件
date:  2019-10-28 17:09:49
tags:
- linux
- log
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

背景：我使用的是windows机器（装有xshell），想要获取产线某个server的tomcat日志文件

```
涉及3台server
（1）localhost: windows本机
（2）10.250.10.1: linux server 1 （有root权限）
（3）10.250.10.2: linux server 2 （没有root权限，但是可以使用sudo su切换成root权限）
1. xshell登录10.250.10.1	        #先登录10.250.10.1（作为跳板）
2. ssh jalchu@10.250.10.2	#使用普通账号登录10.250.10.2
3. sudo su			#切换权限，此时是10.250.10.2的root用户权限
4. cp /opt/apache-tomcat_1/logs/xxx.log /tmp	#复制文件到temp文件夹
5. chmod -R 777 /tmp/xxx.log	#修改文件权限
6. exit   			#退出root，此时是10.250.10.2的普通用户权限
7. exit				#退出10.250.10.2，此时返回10.250.10.1，并且是root用户权限
8. scp jalchu@10.250.10.2:/tmp/xxx.log ./ 	#复制10.250.10.2的文件到10.250.10.1当前目录
9. 使用xftp传输10.250.10.1文件到windows本地
```
