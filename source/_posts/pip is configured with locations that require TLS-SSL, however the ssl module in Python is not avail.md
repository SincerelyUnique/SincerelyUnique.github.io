---
title: pip is configured with locations that require TLS-SSL, however the ssl module in Python is not avail
date: 2019-08-09 16:06:11
tags:
- python
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
# 查看旧版本号
openssl version
 
# 下载openssl，解压，进入根目录
wget https://www.openssl.org/source/openssl-1.1.1a.tar.gz
tar -zxvf openssl-1.1.1a.tar.gz
cd openssl-1.1.1a
 
# 编译安装
./config --prefix=/usr/local/openssl no-zlib
make
make install
 
# 创建链接
ln -s /usr/local/openssl/include/openssl /usr/include/openssl
ln -s /usr/local/openssl/lib/libssl.so.1.1 /usr/local/lib64/libssl.so
ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl
 
# 写入动态库配置文件
echo "/usr/local/openssl/lib" >> /etc/ld.so.conf
 
# 扫描动态链接库链接名字，使之生效
ldconfig -v
 
# 查看新版本号
openssl version
```

```
# 重装python
./configure --with-openssl=/usr/local/openssl
make
make install
```
