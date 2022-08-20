---
title: ubuntu安装配置postgreSQL
date: 2015-10-29 08:54:37
tags:
- postgreSQL
categories:
- [学习, DevOPS, Ubuntu]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 在Ubuntu下安装Postgresql
## 使用 apt-get install 安装
```
$ sudo apt-get install -y postgresql-9.1 postgresql-client-9.1 postgresql-contrib-9.1 postgresql-server-dev-9.1
（使用新立得软件包管理器搜索postgreSQL，并安装，我安装的是9.3）
```

# 修改PostgreSQL数据库的默认用户postgres的密码
## 使用postgres用户登录psql客户端
```
$ sudo -u postgres psql
```

## 修改PostgreSQL默认用户postgres的密码（postgres=#为psql中的命令行）

```
postgres=# ALTER USER postgres WITH PASSWORD 'postgres';
```

## 退出PostgreSQL的psql客户端

```
postgres=# \q
```

# 修改Linux系统的postgres用户的密码

## 设置PostgreSQL用户密码

PostgreSQL数据库默认会创建一个Linux用户postgres，通过下面的代码修改密码为'postgres’。

```
$ sudo -u postgres passwd
    输入新的 UNIX 密码：
    重新输入新的 UNIX 密码：
```

现在就可以在数据库服务器上，用postgres帐号通过psql或者pgAdmin等等客户端操作数据库了。

（参考：http://www.360doc.com/content/13/0822/10/10384031_309039914.shtml）

# 修改PostgresSQL数据库配置实现远程访问

## 监听任何地址访问，修改连接权限

将/etc/postgresql/9.1/main/postgresql.conf文档中如下注释去掉：

```
    #listen_addresses = ‘localhost’ 改为 listen_addresses = ‘*’
    #password_encryption = on 改为 password_encryption = on
```

在/etc/postgresql/9.1/main/pg_hba.conf文档末尾加上如下内容：

```
    # to allow your client visiting postgresql server
    host all all 0.0.0.0 0.0.0.0 md5
```
## 重启PostgreSQL数据库

```
$ /etc/init.d/postgresql restart
```

# 管理PostgreSQL用户和数据库

## 登录postgre SQL数据库

```
$ psql -U postgres -h 127.0.0.1
```

## 创建新用户test，密码123456，无建数据库的权限

```
$ postgres=# create user “test” with password ‘123456’ nocreatedb;
```

## 建立数据库，并指定所有者

```
$ postgres=# create database “testdb” with owner=”test”;
```

# 安装postgresql数据库pgAdmin3客户端管理程序

```
$ apt-get install -y pgadmin3
```

（全文参考：http://blog.sina.com.cn/s/blog_6af33caa0100ypck.html）


