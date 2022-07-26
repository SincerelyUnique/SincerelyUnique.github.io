---
title: linux软件包管理（兄弟连）
date: 2022-07-22 14:34:43
tags:
categories:
valine:
  placeholder: "bla bla"
---

# 简介

## 软件包分类
- 源码包（含脚本安装包）：大部分都是c语言编写，安装比较慢，容易报错。
- 二进制包（含rpm包、系统默认包）：目前linux主要安装方式，源代码包经过编译后的二进制机器语言包（0101），好处是安装时间比较慢，节约时间，一般厂家预先编译，无法阅读源代码
- redhat系列和debian系列最大的不同就是软件包管理不同：redhat-rpm，debian-deb
- 脚本安装包：不多见，类似windows中的安装程序，有窗口界面，点击下一步下一步，其实就是给源码包写入一个安装程序

## 源码包优点
- 开源，linux比windows晚但是能够成为服务器主流的一个原因
- 可以自由选择所需功能，windows就比较少
- 软件是编译安装，更适合系统，更稳定，效率更高，通常源码包安装，有统计，相同版本相同系统使用源码安装通常比rpm安装提高5%左右效率
- 卸载方便，可以直接删源码包安装目录，windows不行（会遗留大部分垃圾文件，导致c盘臃肿）

## 源码包缺点
- 安装步骤多，尤其大型软件集合（如lamp环境，邮件服务器）
- 编译时间长，比二进制安装时间长（比如源码包安装linux系统要大概几天时间）
- 编译过程容易报错，对新手不友好

## rpm包优点
- 包管理系统简单，只通过几个命令就可以实现包的安装、升级、查询和卸载
- 安装速度比源码包安装快的多，和windows的安装速度差不多

## rpm包缺点
- 预先编译，看不到源代码
- 功能选择不如源码包灵活
- 依赖性，先装a在装b在装c，卸载先卸载c在卸载b在卸载a

# rpm命令管理

## 命名规则
以 `httpd-2.2.15-15.el6.centos.1.i686.rpm` 为例
- httpd  -  软件包名
- 2.2.15 -  软件版本
- 15     -  软件发布的次数
- el6.centos  -  适合的linux平台，rhel6和centos两个平台
- i686  -  适合的硬件平台，x64指64位软件包
- rpm  -  rpm包扩展名，其实linux没有扩展名这个概念的，但是rpm包必须要标注

## 依赖性
如执行 `rpm -ivh mysql-connector-odbc-5.1.5r1144-7.el6.i686.rpm` 报依赖错误
- 树形依赖： a -> b -> c
- 环形结构： a -> b -> c -> a, 可以把软件包a,b,c一块装
- 模块依赖： 模块依赖查询网站 - www.rpmfind.net (碰到库文件依赖可以使用该网站，如搜索libodbcinst.so.2这个库文件)

## 包全名与包名
- 包全名：操作的包是没有安装的软件包时，使用包全名。而且要注意路径
- 包名：操作已经安装的软件包时，使用包名。是搜索/var/lib/rpm/中的数据库

## 安装
命令 `rpm -ivh 包全名`，选项：
- -i： install，安装
- -v： verbose，显示详细信息
- -h： hash，显示进度
- --nodeps：不检测依赖性，有可能造成底层功能缺失，导致软件不能使用，少用，多为实验用

## 升级
命令 `rpm -Uvh 包全名`，选项：
- -U：upgrade，升级

## 卸载
命令 `rpm -e 包名`，选项：
- -e：erase，卸载
- --nodeps，不检查依赖性

## 查询是否安装
命令 `rpm -qa 包名` 或 `rpm -qa|grep httpd`，选项：
- -q：query，查询，查询包是否安装
- -a：all，所有，查询所有已经安装的rpm包

## 查询软件包详细信息
命令 `rpm -qi 包名`，选项：
- -i：information，查询软件信息
- -p：package，查询未安装包信息（-p后面跟包全名，$ rpm -qip 包全名）

## 查询包中文件安装位置
我们推荐使用默认安装位置，不要手工改动rpm默认安装位置，命令 `rpm -ql 包名`，选项：
- -l：list，i二标
- -p：package，查询未安装包信息（$ rpm -qlp 包全名）

## 查询系统文件属于哪个rpm包
命令 `rpm -qf 系统文件名`， 选项：
- -f：file，查询系统文件属于哪个软件包

## 查询软件包的依赖性
命令 `rpm -qR 包名`，选项：
- -R：requires，查询软件包的依赖性
- -p：package，查询未安装包信息（$ rpm -qRp 包全名）

## RPM包校验
判断文件丢包或篡改，命令 `rpm -V 已安装的包名`，选项：
- -V：verify，校验指定的RPM包中的文件

验证内容中的8个信息的具体内容如下：
|标志|说明|
|---|---|
|S|文件大小是否改变|
|M|文件的类型或文件的权限rwx是否被改变|
|5|文件MD5校验和是否改变（可以看成文件内容是否改变）|
|D|设备的中，从代码是否改变|
|L|文件路径是否改变|
|U|文件的所有者是否改变|
|G|文件的属组是否改变|
|T|文件的修改时间是否改变|

文件类型：
|标志|说明|
|---|---|
|c|配置文件（config file）|
|d|普通文档（doumentation）|
|g|“鬼”文件（ghost file）。很少见，就是该文件不应该被这个RPM包包含|
|l|授权文件（license file）|
|r|描述文件（read me）|

## RPM包中文件提取
只提取RPM包中某个指定文件，比如安装了某个RPM包后，因操作误删除了该包下面某个配置文件，此时我们不想重新安装这个rpm包，只需要提取一下这个配置文件覆盖，主要用来修复误操作，命令 `rpm2cpio 包全名 | cpio -idv .文件绝对路径`，rpm2cpio是将rpm包转换为cpio格式的命令，cpio是一个标准工具，它用于创建软件档案文件和从档案文件中提取文件，cpio单独使用命令 `cpio 选项 < [文件|设备]` （< 是输入重定向），选项：
- -i：copy-in模式，还原
- -d：还原时自动新建目录
- -v：显示还原过程

练习：
```bash
[root@core-pods-3 ~]# rpm -qf /bin/ls   # 查询ls命令属于哪个软件包
coreutils-8.22-24.el7_9.2.x86_64
[root@core-pods-3 ~]# mv /bin/ls /tmp/  # 造成ls命令误删除假象
[root@core-pods-3 ~]# rpm -qa|grep coreutils   # 查询rpm包版本
policycoreutils-2.5-34.el7.x86_64
coreutils-8.22-24.el7_9.2.x86_64
[root@core-pods-3 ~]# locate coreutils-8.22-24.el7_9.2.x86_64  # yum install -y mlocate
[root@core-pods-3 ~]# rpm2cpio /mnt/cdrom/Packages/coreutils-8.4-19.el6.i686.rpm | cpio -idv ./bin/ls  # 提取rpm包中ls命令到当前目录的/bin/ls下
[root@core-pods-3 ~]# cp ./bin/ls /bin/  # 把ls命令复制回/bin目录，修复文件丢失
```

# yum在线管理
这里的“在线”并不是说yum一定要联网，也可以用光盘，redhat需要付费才能使用yum，yum命令管理的是rpm包，yum最好的地方是解决依赖性。

## ip地址配置
> ifconfig eth0 192.168.1.156
```
[root@core-pods-3 ~]# setup     # 使用setup工具
[root@core-pods-3 ~]# vi /etc/sysconfig/network-scripts/ifcfg-eth0   # 把ONBOOT='no'改为'yes'，启动网卡
[root@core-pods-3 ~]# service network restart
```

## 网络yum源
> [root@core-pods-3 ~]# vi /etc/yum.repos.d/CentOS-Base.repo  # 注意dvd yum源配置：CentOS-Media.repo
- [base]：容器名称，一定要放在[]中，一般不改，想改也可以改
- name：容器说明，可以自己随便写
- mirrorlist：镜像站点，这个可以注释掉，和baseurl性质差不多
- baseurl：我们的yum源服务器的地址。默认是centos官方的yum源服务器，是可以使用的，如果你觉得慢可以改成你喜欢的yum源地址
- enabled：此容器是否生效，如果不写或写成 enable=1 都是生效，写成 enable=0 就是不生效
- gpgcheck：如果是 1 是指rpm的数字证书生效，如果是 0 则不生效
- gpgkey：数字证书的公钥文件保存位置。不用修改

## yum命令
```bash
[root@core-pods-3 yum.repos.d]# yum list   # 查询远程服务器上所有可用软件包列表（联网）
[root@core-pods-3 yum.repos.d]# yum search nginx  # 搜索服务器上所有和nginx有关的包
[root@core-pods-3 yum.repos.d]# yum -y install nginx  # install，-y 自动回答yes
[root@core-pods-3 yum.repos.d]# rpm -q nginx
[root@core-pods-3 yum.repos.d]# yum -y update nginx   # update， -y 自动回答yes
[root@core-pods-3 yum.repos.d]# yum -y update    # 谨慎执行该操作，这个会更新linux内核
[root@core-pods-3 yum.repos.d]# yum -y remove nginx   # remove, -y自动回答yes，尽量少用该命令，有可能因为依赖导致一些问题

# yum软件组管理命令
[root@core-pods-3 yum.repos.d]# yum grouplist   # 列出所有可用的软件组列表
[root@core-pods-3 yum.repos.d]# yum groupinstall 软件组名    # 安装指定软件组，组名可以由grouplist查询出来
[root@core-pods-3 yum.repos.d]# yum groupremove 软件组名   # 卸载指定软件组
```

## 光盘yum源搭建
主要针对一些无法访问互联网的环境，使用本地光盘作为yum源，缺点是光盘中的软件不一定是最新的。
```bash
#（1）挂载光盘
[root@core-pods-3 ~]# mount /dev/cdrom /mnt/cdrom/     
[root@core-pods-3 ~]# mount    # 查看挂载

#（2）让网络yum源文件失效（删除或修改文件后缀，或者将文件里每个源的enable配置项改为0）
[root@core-pods-3 ~]# cd /etc/yum.repos.d/
[root@core-pods-3 yum.repos.d]# mv CentOS-Base.repo CentOS-Base.repo.bak
[root@core-pods-3 yum.repos.d]# mv CentOS-Debuginfo.repo CentOS-Debuginfo.repo.bak
[root@core-pods-3 yum.repos.d]# mv CentOS-Vault.repo CentOS-Vault.repo.bak

#（3）修改光盘yum源文件
[root@core-pods-3 yum.repos.d]# vim CentOS-Media.repo
[c7-media]
name=CentOS-$releasever - Media
baseurl=file:///mnt/cdrom       # 地址为你自己的光盘挂载地址
gpgcheck=1
enabled=1     # 这里改为1使之生效
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
[root@core-pods-3 yum.repos.d]# yum list   #  保存上面内容后重新检查yum源
```

# 源码包管理

## 源码包与rpm包的区别
- 安装之前的区别：概念上的区别，比如源码包安装慢，有可能报错，儿rpm包相反
- 安装之后的区别：安装位置不同，所以后续管理不同

**rpm包默认安装路径**
|目录|说明|
|---|---|
|/etc/|配置文件安装目录|
|/usr/bin/|可执行的命令安装目录|
|/usr/lib/|程序所使用的函数库保存位置|
|/usr/share/doc/|基本的软件使用手册保存位置|
|/usr/share/man/|帮助文件保存位置|

> [root@core-pods-3 ~]# rpm -ql nginx   # 查看rpm安装后各个文件位置

**源码包安装位置**
源码包可以安装在指定位置中，一般是/usr/local/软件名/

**管理上的不同** rpm包安装的服务可以使用系统服务管理命令（service）来管理，例如rpm包安装的apache的启动方法是：`/etc/rc.d/init.d/httpd start` 或者 `service httpd start`。service会自动去rpm包安装路径下找httpd这个命令。而源码包安装的服务则不能被服务管理命令管理，因为没有安装到默认路径中。所以只能以绝对路径进行服务的管理，如：`/usr/local/apache2/bin/apachectl start`。

## 源码包安装过程
1. 安装c语言编译器gcc
```bash
[root@core-pods-3 init.d]# yum install -y gcc
[root@core-pods-3 init.d]# rpm -q gcc
[root@core-pods-3 httpd-2.4.54]# gcc --version  # 我的是安装系统时自带的4.8.5
```
2. 下载源码包 [httpd](https://httpd.apache.org/download.cgi)
```bash
[root@core-pods-3 httpd]# wget https://dlcdn.apache.org/httpd/httpd-2.4.54.tar.gz --no-check-certificate
```
3. 注意事项
   - 源码包保存位置：/usr/local/src/
   - 软件安装位置：/usr/local/
   - 如何确定安装过程报错：
     - 安装过程停止
     - 并出现error、warning或no的提示

4. 解压缩下载的源码包
```bash
[root@core-pods-3 httpd]# tar -zxvf httpd-2.4.54.tar.gz 
[root@core-pods-3 httpd]# du -sh httpd-2.4.54   # 看一下解压后多大
[root@core-pods-3 httpd]# cd httpd-2.4.54  
```
5. 进入解压包内，可以先查看INSTALL安装说明和README说明（可以直接照里面写的操作做），如果遇到一些问题也可以点[这里](https://developpaper.com/source-installation-apache-problems-encountered-and-solutions/)，**注意**：如果你的生产环境用户量达到百万甚至千万级别，通常使用源码包安装相比于rpm包安装可以提高大概5%左右效率，这种情况下可以试着使用源码包安装方式。否则还是使用rpm包安装。
   - ./configure 软件配置与检查
     - 定义需要的功能选项
     - 检测系统环境是否符合安装要求
     - 把定义好的功能选项和检测系统环境的信息都写入Makefile文件，用于后续的编辑
```bash
[root@core-pods-3 httpd-2.4.54]# ./configure --help
[root@core-pods-3 httpd-2.4.54]# ./configure --prefix=/usr/local/apache2   # 这一步可能会报APR错误，检查INSTALL文档，发现我们要先安装APR

# 安装APR（如果上一步没APR报错，则省略下述步骤）
[root@core-pods-3 httpd-2.4.54]# cd srclib/
[root@core-pods-3 srclib]# wget https://dlcdn.apache.org//apr/apr-1.7.0.tar.gz --no-check-certificate
[root@core-pods-3 srclib]# wget https://dlcdn.apache.org//apr/apr-util-1.6.1.tar.gz --no-check-certificate
[root@core-pods-3 srclib]# tar -zxvf apr-1.7.0.tar.gz
[root@core-pods-3 srclib]# tar -zxvf apr-util-1.6.1.tar.gz
[root@core-pods-3 srclib]# mv apr-1.7.0 apr
[root@core-pods-3 srclib]# mv apr-util-1.6.1 apr-util
[root@core-pods-3 srclib]# cd ../

# 继续安装httpd
[root@core-pods-3 httpd-2.4.54]# ./configure --prefix=/usr/local/apache2    # 等吧，等待重新写入Makefile
[root@core-pods-3 httpd-2.4.54]# ./configure --with-apr=/usr/local/httpd/httpd-2.4.54/srclib/apr --with-apr-util=/usr/local/httpd/httpd-2.4.54/srclib/apr-util --prefix=/usr/local/apache2  # 和上面命令功能一样
[root@core-pods-3 httpd-2.4.54]# ls   # 可以看到生成了Makefile
[root@core-pods-3 httpd-2.4.54]# make   # 编译，调用gcc，然后等

# 解决上一步编译报错：xml/apr_xml.c:35:19: fatal error: expat.h: No such file or directory
[root@core-pods-3 httpd-2.4.54]# yum -y install expat-devel
[root@core-pods-3 httpd-2.4.54]# make  # 重新编译

# 继续报错，apr-util缺少xml相关库：undefined reference to `XML_GetErrorCode' ...
[root@core-pods-3 httpd-2.4.54]# yum install -y libxml2-devel  # 安装xml相关库
[root@core-pods-3 httpd-2.4.54]# ./configure --prefix=/usr/local/apache2   # 重新写入配置
[root@core-pods-3 httpd-2.4.54]# make   # 如果报错可以执行make clean清理
[root@core-pods-3 httpd-2.4.54]# make install  # 编译安装，真正向/usr/local/apache2目录写文件
[root@core-pods-3 httpd-2.4.54]# /usr/local/apache2/bin/apachectl start   # 启动，测试：浏览器输入你的ip后显示It works!
[root@core-pods-3 apache2]# vi /usr/local/apache2/htdocs/index.html  # 随便改一下刷新页面，可以看到内容变化
[root@core-pods-3 apache2]# rm -rf /usr/local/apache2/  # 源码包卸载直接删目录即可，不会有多余垃圾
```

# 脚本安装包
## 特点
- 脚本安装包并不是独立的软件包类型，常见安装的是源码包
- 是人为把安装过程写成了自动安装的脚本，只要执行脚本，定义简单的参数，就可以完成安装
- 非常类似于windows下软件的安装方式
## 以安装Webmin为例，什么是Webmin？
- Webmin是一个基于web的linux系统管理界面。您可以通过图形化的方式设置用户账号、Apache、DNS文件共享等服务。
## Webmin安装过程
- 下载软件，`wget https://sourceforge.net/projects/webadmin/files/webmin/1.997/webmin-1.997.tar.gz --no-check-certificate`
- 解压缩，并进入压缩目录
- 执行安装脚本`[root@core-pods-3 webmin-1.997]# ./setup.sh`
