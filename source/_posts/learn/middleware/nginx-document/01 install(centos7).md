---
title: (1) Install(Centos7)
date: 2020-12-25 15:59:07
categories:
- [学习, 中间件学习, Nginx文档学习]
tags:
- nginx
---

```
参考：
http://nginx.org/en/docs/install.html
http://nginx.org/en/linux_packages.html
 
nginx在每个系统上的安装方式可能不尽相同，比如ubuntu可以使用sudo安装而centos可以使用yum。
并且在相同系统上也可以选择不同方式安装，比如centos使用yum安装或者编译source code安装。
这里以centos7.3为例，选择yum安装
 
1.先决条件
[root@jalen ~]# sudo yum install yum-utils
 
2.创建yum仓库
[root@jalen ~]# cd /etc/yum.repos.d
[root@jalen yum.repos.d]# touch nginx.repo
[root@jalen yum.repos.d]# vi nginx.repo
 
3.编辑nginx.repo写入以下内容
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
 
[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
 
4.仓库默认使用stable版，如果想尝试mainline版，执行下面命令
[root@jalen yum.repos.d]# sudo yum-config-manager --enable nginx-mainline
 
5.安装
[root@jalen yum.repos.d]# sudo yum install nginx
[root@jalen yum.repos.d]# whereis nginx
 
6.其他linux发行版例如Debian、Ubuntu、SLES、Alpine参考：
http://nginx.org/en/linux_packages.html#Debian
http://nginx.org/en/linux_packages.html#Ubuntu
http://nginx.org/en/linux_packages.html#SLES
http://nginx.org/en/linux_packages.html#Alpine
 
 
 
附录：
1.编译安装nginx：下载nginx某个分支源码包
选择一个nginx分支，注意default是mainline版，stable-*是LTS稳定版，分支可从下面两个地址中选择
（1）官网地址：http://hg.nginx.org/pkg-oss/branches  （点击进入一个分支，点击左边菜单zip或gz下载源码包）
（2）Github地址：https://github.com/nginx/nginx  （Switch branches and clone）
编译配置：http://nginx.org/en/docs/configure.html （参数说明文档）
./configure
    --sbin-path=/usr/local/nginx/nginx
    --conf-path=/usr/local/nginx/nginx.conf
    --pid-path=/usr/local/nginx/nginx.pid
    --with-http_ssl_module
    --with-pcre=../pcre-8.44
    --with-zlib=../zlib-1.2.11
 
 
 
备注：
1.nginx有两个版本，一个称为Mainline版本，一个称为Stable版本，区别如下：
（1）Mainline版本，这个版本包括最新的功能和bug修复，并且在发布下一个版本时，
     所有的新功能都会优先出现在这个版本中，所以这个版本包括一些实验模块，
     而且它也可能有一些新的bug。--主线版本
（2）Stable版本，这个版本中所有的功能可能不是最新的，但是关键性的bug还是修复了的，
     由于这个版本中的功能会优先在Mainline版本中测试，所以此版本较为稳定，
     也推荐用于生产环境。 --稳定版本LTS
（参考：https://zhuanlan.zhihu.com/p/225401052）
 
 
2.linux发行版
linux操作系统的内核于1991年10月5日由Linus Torvalds首次发布
linux发行版：包含商业版和免费版，如centos、debian、ubuntu、fedora、SLES、Alpine等
商业版：商业公司维护，以Redhat为代表，如RHEL（RedHat Enterprise Linux），support费用很昂贵
免费版：开源社区维护，以debian为代表，大部分VPS（Virtual Private Server）会预置这些免费的OS
CentOS：RHEL对应的开源版，与Redhat同源，发行会滞后于RedHat，且不单独提供商业支持，体积较Debian大，
        不过紧随RHEL，帮助文档和技术资料较多，是成熟的Linux发行版，比较可靠。
        （全称：Community Enterprise Operating System）
Debian：比Ubuntu稳定，体积小，128M的VPS即可以流畅运行Debian，缺点是帮助文档和技术资料相对稀少
Ubuntu：桌面操作系统，debian的派生版，较少用于服务器操作系统，毕竟桌面会占用内存和磁盘空间
Fedora：基于redhat，对外开放，版本迭代迅速且支持较短，视为“新技术的试验场”
SLES：SUSE Linux Enterprise Server，德国SuSE Linux AG公司发行维护的Linux发行版
Alpine：由社区开发的基于musl和BusyBox的Linux操作系统，以安全为理念面向x86路由器、防火墙、虚拟专用网、IP电话盒及服务器而设计。
如何选择：新手玩CentOS，高手玩debian，有钱人玩ubuntu，喜欢爬坑玩Fedora
（参考：https://zhuanlan.zhihu.com/p/32274264）
 
 
3.nginx（主）软件包内置了所有modules，从而避免了额外的依赖。不过从nginx1.9.11开始，nginx对
动态模块（dynamic modules）提供了支持，以下modules可以使用动态构建，并作为独立的软件包
nginx-module-geoip
nginx-module-image-filter
nginx-module-njs
nginx-module-perl
nginx-module-xslt
（参考load_module配置：http://nginx.org/en/docs/ngx_core_module.html#load_module）
 
 
4.nginx签名（Signatures），PGP keys和GPG key，yum安装rpm时需要，默认如下
Retrieving key from https://nginx.org/keys/nginx_signing.key
Importing GPG key 0x7BD9BF62:
 Userid     : "nginx signing key <signing-key@nginx.com>"
 Fingerprint: 573b fd6b 3d8f bc64 1079 a6ab abf5 bd82 7bd9 bf62
 From       : https://nginx.org/keys/nginx_signing.key
Is this ok [y/N]: y
（参考：http://www.dewinter.com/gnupg_howto/english/GPGMiniHowto-1.html）
 
 
 
其他：
centos修改hostname
[root@chumingcheng ~]# hostnamectl set-hostname jalen
[root@chumingcheng ~]# hostname
 
 
```
