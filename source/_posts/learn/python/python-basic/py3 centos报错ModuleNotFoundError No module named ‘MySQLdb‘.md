---
title: py3 centos报错ModuleNotFoundError No module named ‘MySQLdb‘
date: 2021-05-23 21:15:20
tags:
- mysql
- linux
categories:
- [学习, Python语言学习, Python]
---

ref：https://github.com/PyMySQL/mysqlclient#linux

```bash
Note that this is a basic step. I can not support complete step for build for all environment. If you can see some error, you should fix it by yourself, or ask for support in some user forum. Don't file a issue on the issue tracker.
You may need to install the Python 3 and MySQL development headers and libraries like so:
$ sudo apt-get install python3-dev default-libmysqlclient-dev build-essential # Debian / Ubuntu
% sudo yum install python3-devel mysql-devel # Red Hat / CentOS
Then you can install mysqlclient via pip now:
$ pip install mysqlclient
```
