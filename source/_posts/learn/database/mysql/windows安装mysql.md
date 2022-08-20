---
title: windows10安装mysql
date: 2021-10-27 02:51:43
tags: mysql
categories:
- [学习, 数据库学习, MySQL]
---

> Windows10安装mysql，通过zip包方式安装，因为mysql installer 总是安装进度到88%时failed，然后跳到50%，无论是使用老版本的mysql installer还是更老的都不行，尝试安装vs++也不行

# 操作

## 下载

地址：https://dev.mysql.com/downloads/mysql/ 

## 解压

随便解压到某个路径即可，我这里在D盘下新建mysql，将其解压到这里

## 安装

左下角放大镜搜索cmd，右键以管理员身份运行命令行

执行步骤如下

```
Microsoft Windows [Version 10.0.19042.1288]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>d:

D:\>cd D:\mysql\mysql-5.7.35-winx64\bin

D:\mysql\mysql-5.7.35-winx64\bin>mysqld.exe -install
Service successfully installed.

D:\mysql\mysql-5.7.35-winx64\bin>mysql.exe
ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost' (10061)

D:\mysql\mysql-5.7.35-winx64\bin>mysqld.exe --initialize
2021-10-27T02:46:32.564523Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2021-10-27T02:46:32.565974Z 0 [ERROR] --initialize specified but the data directory has files in it. Aborting.
2021-10-27T02:46:32.566210Z 0 [ERROR] Aborting


D:\mysql\mysql-5.7.35-winx64\bin>mysqld.exe --initialize

D:\mysql\mysql-5.7.35-winx64\bin>
```

主要是两个命令：

`mysqld --install`

将mysql添加到windows services，执行后可以alt+R输入services.msc，就可以在服务列表查看到mysql服务了，
但是此时尝试服务启动会失败，需要执行下面命令

`mysqld --initialize`

执行后会在mysql根路径生成data文件夹以及一些必要文件，此时可以启动mysql服务了

## 登录

参考：https://dbschema.com/2020/04/21/mysql-default-username-password/

1. stop mysql service
2. create a mysql-init.txt file and write: `SET PASSWORD FOR 'root'@'localhost' = PASSWORD('new_password');`
3. run cmd and execute: `mysqld --init-file=C:\\mysql-init.txt`
4. ctrl + c terminate the cmd and start mysql service, then login with new_password
