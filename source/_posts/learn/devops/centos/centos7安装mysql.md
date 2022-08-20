---
title: centos7安装mysql
date: 2018-04-18 10:22:09
tags:
- linux
- mysql
categories:
- [学习, DevOPS, CentOS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```bash
sudo yum install wget
 
wget http://repo.mysql.com/mysql57-community-release-el7-8.noarch.rpm
 
sudo rpm -ivh mysql57-community-release-el7-8.noarch.rpm
 
sudo yum install mysql-server
```

```bash
mysql -uroot -p
root
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)
sudo chown -R root:root /var/lib/mysql
service mysqld restart
Enter password: root
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
cat /var/log/mysqld.log  | grep password
2018-04-18T02:06:03.625052Z 1 [Note] A temporary password is generated for root@localhost: 1,,F6rSt?Q_I
2018-04-18T02:06:31.479870Z 2 [Note] Access denied for user 'root'@'localhost' (using password: YES)
mysql -u root -p
Enter password: 1,,F6rSt?Q_I
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.21
Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

```bash
mysql> use mysql
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
mysql> SET PASSWORD = PASSWORD('root');
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
mysql> SET PASSWORD = PASSWORD('root123456');
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
mysql> SET PASSWORD = PASSWORD('1qaz@wsx');
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
mysql> SET PASSWORD = PASSWORD('1qaz@wsxCMC');
Query OK, 0 rows affected, 1 warning (0.00 sec)
 
mysql> 
```

```bash
# authorize to remote login
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'IDENTIFIED BY 'login_password' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.01 sec)
 
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

centos6安装mysql时遇到glibc版本升级问题，参考：http://cnodejs.org/topic/56dc21f1502596633dc2c3dc
