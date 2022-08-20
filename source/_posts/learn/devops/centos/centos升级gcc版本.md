---
title: centos升级gcc版本
date: 2020-09-03 13:19:24
tags:
- linux
- gcc
categories:
- [学习, DevOPS, CentOS]
---

```
centos升级gcc版本（仅供参考）
当前centos版本：7.6
当前centos gcc版本：4.8.5 （CentOS 7官方源带的gcc最新版本是4.8.5）
升级到centos gcc版本：10.2.0 （latest版本）
 
1. 检查当前centos的gcc版本
[root@hostname gcc-10.2.0]# gcc -v
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/libexec/gcc/x86_64-redhat-linux/4.8.5/lto-wrapper
Target: x86_64-redhat-linux
Configured with: ../configure --prefix=/usr --mandir=/usr/share/man ... ...
Thread model: posix
gcc version 4.8.5 20150623 (Red Hat 4.8.5-39) (GCC) 
 
[root@hostname gcc-10.2.0]# yum info gcc
Loaded plugins: fastestmirror, post-transaction-actions
Loading mirror speeds from cached hostfile
Available Packages
Name        : gcc
Arch        : x86_64
Version     : 4.8.5
Release     : 4.el7
Size        : 16 M
Repo        : prod_os_centos7.2_64
Summary     : Various compilers (C, C++, Objective-C, Java, ...)
URL         : http://gcc.gnu.org
License     : GPLv3+ and GPLv3+ with exceptions and GPLv2+ with exceptions and LGPLv2+ and BSD
Description : The gcc package contains the GNU Compiler Collection version 4.8.
            : You will need this package in order to compile C code.
 
 
 
2. 下载安装
download addr: https://gcc.gnu.org/mirrors.html  (根据你的地理位置选择镜像)
download compress package: gcc-10.2.0.tar.gz  (截至2020-08-31最新版本)
 
[root@hostname gcc-10.2.0]# wget http://ftp.tsukuba.wide.ad.jp/software/gcc/releases/gcc-10.2.0/
[root@hostname gcc-10.2.0]# mkdir gcc-10.2.0-build
[root@hostname gcc-10.2.0]# mv gcc-10.2.0.tar.gz gcc-10.2.0-build
[root@hostname gcc-10.2.0]# cd gcc-10.2.0-build
[root@hostname gcc-10.2.0]# tar -zxvf gcc-10.2.0.tar.gz
[root@hostname gcc-10.2.0]# cd gcc-10.2.0
[root@hostname gcc-10.2.0]# ./contrib/download_prerequisites   (If you obtained GMP, MPFR and/or MPC... ...)
[root@hostname gcc-10.2.0]# ./configure --enable-languages=c,c++ --disable-multilib
[root@hostname gcc-10.2.0]# make
[root@hostname gcc-10.2.0]# make install
 
 
 
3. 遇到的问题
（1）Building GCC requires GMP 4.2+, MPFR 2.3.1+ and MPC 0.8.0+
解决：在gcc-10.2.0目录下执行./contrib/download_prerequisites
参考：https://stackoverflow.com/questions/9253695/building-gcc-requires-gmp-4-2-mpfr-2-3-1-and-mpc-0-8-0
 
（2）安装参考
https://medium.com/@bipul.k.kuri/install-latest-gcc-on-centos-linux-release-7-6-a704a11d943d
https://www.softwarecollections.org/en/scls/rhscl/devtoolset-7/
 
（3）CentOS 如何查看已经安装了哪些源？
[root@hostname gcc-10.2.0]# yum repolist
[root@hostname gcc-10.2.0]# ls /etc/yum.repos.d/
[root@hostname gcc-10.2.0]# yum repolist all
[root@hostname gcc-10.2.0]# yum -q list \*release\*
 
（4）Bash scl command not found
[root@hostname gcc-10.2.0]# yum install scl-utils
 
（5）No package centos-release-SCL available, Error: Nothing to do
需要添加相关yum源
 
（6）yum-config-manager命令找不到的解决方法
[root@hostname gcc-10.2.0]# yum -y install yum-utils
```
