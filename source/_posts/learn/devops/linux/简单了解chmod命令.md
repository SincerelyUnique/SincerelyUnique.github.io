---
title: 简单了解Linux chmod命令
date: 2017-02-22 14:34:43
tags:
- linux
categories:
- [学习, DevOPS, Linux]
---

@[TOC](简单了解Linux chmod命令的使用)

# 说明
chmod 主要用来对文件或者文件夹重新授权
# 语法
> chmod [reference][operator][mode] file... 

##  reference（角色）
Reference |  Class  |   Description
----| ----------| -----
u   | owner |  文件所有者，通常为该文件创建人。<br>（eg: chmod u=rwx filename）
g   | group  |  文件组，改组所有成员共享当前设置的权限。<br>（eg: chmod g=rx filename）
o   | others |  既不是文件所有者，也不是文件组成员，通常指剩余用户。<br>(eg: chmod o=wx filename)
a  |        all |   包含以上所有角色成员, 也可以写成ugo。<br>(eg: chmod a=rwx filename 或者chmod ugo=rwx filename)

## operator（操作）
Operator | Description
--- | ---
+    |     为当前角色添加相应操作权限
-     |    为当前角色移除相应操作权限
=     |    直接赋权
**注意** : 中间不要加空格.

## modes（想要赋予的权限）
Mode | Class | Description
--- | --- | ---
r   |  read  | 读权限
w  |  write  | 写、删权限（对于文件夹则可以在其中创建file）
x   |  execute  | 可执行权限，比如赋予shell脚本可执行权限

## 使用ls -l查看文件（夹）相应权限
```bash
- rw- rw- r--  mik  mik    assgn1_client.c
- rw- rw- r--  mik  mik    assgn1_server.c
d rwx rwx r-x  mik  mik    EXAM
- rw- rw- r--  mik  mik    raw.c
- rwx r-x r-x  mik  mik    header.sh
... so on...
```
- 第一列，d代表directory，即文件夹目录，-代表当前是一个文件
- 第二列代表该文件owner所拥有的权限，比如第一行第二列的rw-代表mik对文件assgn1_client.c有读（r）和写（w）的权限，但是没有可执行权限x
- 第三列代表group所拥有的权限，比如第一行第三列为rw-，代表在mik group的其他人可以读写assgn1_client.c，但不能执行该文件
- 第四列代表others可以操作的权限
## 数字755，777的含义
每个文件和文件夹都包含控制权限，可以用数字来表示这种权限。最基本的就是“000”，即没有授予任何权限。 
Mode | Class | Number
-- | -- | --
r | read | 4
w| write | 2
x| Execute | 1

计算如下：
rx = 4+1 = 5
rwx = 4+2+1 = 7

0 – no permission
1 – execute
2 – write
3 – write and execute
4 – read
5 – read and execute
6 – read and write
7 – read, write, and execute

以777为例，
第一个7代表owner的权限，即read, write, and execute
第二个7代表group的权限，也是read, write, and execute
第三个7代表others的权限，也是read, write, and execute

## 参考
https://www.geeksforgeeks.org/chmod-command-linux/
http://theoryreport.com/technology/linux/understanding-file-permissions-what-does-chmod-777-means.html
