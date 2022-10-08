---
title: MySQL基础001之概述
date: 2022-10-08 11:43:43
tags:
categories:
- [学习, 数据库学习, MySQL]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 概述

## 数据库的好处
1. 持久化数据到本地
2. 可以实现结构化查询，方便管理

## 数据库相关概念
1. DB：数据库，保存一组有组织的数据的容器
2. DBMS：数据库管理系统，又称为数据库软件（产品），用于管理DB中的数据
3. SQL：结构化查询语言，用于和DBMS通信的语言

## 数据库存储数据的特点
1. 将数据放到表中，表再放到库中
2. 一个数据库中可以有多个表，每个表都有一个名字，用来标识自己，表名具有唯一性
3. 表具有一些特性，这些特性定义了数据在表中如何存储，类似java中“类”的设计
4. 表由列组成，我们也成为字段，所有表都是由一个或多个列组成的，每一列类似java中的“属性”
5. 表中的数据是按行存储的，每一行类似java中的“对象”

## SQL的优点
1. 不是某个特定数据库供应商专有的语言，几乎所有DBMS都支持SQL
2. 简单易学
3. 虽然简单，但实际上是一种强有力的语言，灵活使用其语言元素，可以进行非常复杂和高级的数据库操作

## DBMS分为两类
- 基于共享文件系统的DBMS（Access）
- 基于客户机-服务器C/S的DBMS（MySQL、Oracle、SQLServer）

## MySQL服务的启动和停止
- 方式一：计算机-右击管理-服务
- 方式二：通过管理员身份运行
  - net start 服务名（启动服务）
  - net stop 服务名（停止服务）

## 登录和退出MySQL
1. 使用mysql自带的客户端mysql command line，但是只能以root身份登录
2. 通过windows自带的客户端
   - `mysql [-h主机名 -P端口] -u用户名 -p密码`
   - `C:\Windows\system32>mysql -h localhost -P 3306 -u root -p`
   - `C:\Windows\system32>mysql -h localhost -P 3306 -u root -proot`
   - `C:\Windows\system32>mysql -u root -p`
3. 退出：exit

## MySQL的常见命令
```bash
# 查看数据库
mysql> show databases;
# 切换数据库
mysql> use test;
# 查看表
mysql> show tables;
mysql> show tables from mysql;
# 查看当前所在库
mysql> select database();
# 建表
mysql> create table student_info(id int, name varchar(20));
# 查看表结构
mysql> desc student_info;
# 查询表
mysql> select * from student_info;
# 插入表
mysql> insert into student_info(id, name) values(1, 'john');
mysql> insert into student_info(id, name) values(2, 'rose');
# 更新表
mysql> update student_info set name='lilei' where id=1;
# 删除记录
mysql> delete from student_info where id=1;
# 查看mysql版本
mysql> select version();
```

## 查看mysql版本
- 登录模式下执行：`select version();`
- windows命令行下直接执行：
  - `C:\Windows\system32>mysql --version` 
  - `C:\Windows\system32>mysql -V`

## MySQL语法规范
1. 不区分大小写，但建议关键字大写，表名、列表小写
2. 每条命令用分号结尾（命令行登录后可以用\g结尾）
3. 每条命令根据需要，可以进行缩进 或换行
4. 注释
   - 单行注释，`#注释文字`
   - 单行注释，`--注释文字`
   - 多行注释，`/* 注释文字 */`
