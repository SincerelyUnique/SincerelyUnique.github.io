---
title: Linux服务管理（兄弟连）
date: 2022-08-14 14:34:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> 本篇针对centos6，centos7对此有较多改动，以实际为准

# 服务简介与分类

## 服务的分类

从安装方式上来看，linux服务分为两大类：**RPM包默认安装的服务** 和 **源码包安装的服务**，而RPM包安装的服务又分为：**独立的服务**（服务直接在内存中，直接响应用户）和**基于xinetd的服务**（xinetd服务本身独立在内存中，区别于apache服务，我们访问xinetd服务本身是没有任何功能的，真实服务是在掩藏在xinnetd服务后面的真实服务，有点类似管理服务的服务）。

## 服务启动与自启动

windows服务有状态和启动类型，状态分为启动、停止、暂停和恢复，启动类型分为手动、自动和禁用。服务启动就是在当前系统中让服务运行，并提供功能。服务自启动是指让服务在系统开机或重启动之后，随着系统的启动而自动启动服务。

## 查询已安装的服务

1. 查看RPM包安装的服务：`chkconfig --list`，查看服务自启动状态，可以看到所有RPM包安装的服务。
2. 查看源码包安装的服务：查看服务安装位置，一般是/usr/local/下

```bash
# linux运行级别
# 0: 关机
# 1: 单用户
# 2: 不完全多用户
# 3: 字符界面
# 4: 未分配
# 5: 图形界面
# 6: 重启动
[root@core-pods-3 ~]# chkconfig --list    # 这个是查看开机自启动信息，而不是当前正在启动
gwarp           0:off   1:off   2:on    3:on    4:on    5:on    6:off
netconsole      0:off   1:off   2:off   3:off   4:off   5:off   6:off
network         0:off   1:off   2:on    3:on    4:on    5:on    6:off
qemukvmga       0:off   1:off   2:on    3:on    4:on    5:on    6:off
[root@core-pods-3 ~]# ps aux    # 查看当前启动的
[root@core-pods-3 ~]# ps aux | grep httpd
root     19315  0.0  0.2 112824  2348 pts/1    S+   02:33   0:00 grep --color=auto httpd
root     25959  0.0  0.5 224096  6100 ?        Ss   Aug04   0:41 /usr/sbin/httpd -k start
apache   25961  0.0  0.5 224232  6028 ?        S    Aug04   0:00 /usr/sbin/httpd -k start
apache   25962  0.0  0.5 224232  6028 ?        S    Aug04   0:00 /usr/sbin/httpd -k start
apache   25963  0.0  0.5 224232  6028 ?        S    Aug04   0:00 /usr/sbin/httpd -k start
apache   25964  0.0  0.5 224232  6028 ?        S    Aug04   0:00 /usr/sbin/httpd -k start
apache   25965  0.0  0.5 224232  6028 ?        S    Aug04   0:00 /usr/sbin/httpd -k start
apache   25971  0.0  0.5 224232  6028 ?        S    Aug04   0:00 /usr/sbin/httpd -k start
apache   25972  0.0  0.5 224232  6028 ?        S    Aug04   0:00 /usr/sbin/httpd -k start
apache   25973  0.0  0.5 224232  6028 ?        S    Aug04   0:00 /usr/sbin/httpd -k start
apache   28356  0.0  0.5 224232  6028 ?        S    Aug04   0:00 /usr/sbin/httpd -k start
apache   28547  0.0  0.5 224232  6028 ?        S    Aug04   0:00 /usr/sbin/httpd -k start
[root@core-pods-3 ~]# netstat -tlun   # 这种看不到守护进程，守护进程没有端口，
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:29108           0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:12345           0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:10000           0.0.0.0:*               LISTEN     
tcp6       0      0 :::29108                :::*                    LISTEN     
tcp6       0      0 ::1:25                  :::*                    LISTEN     
tcp6       0      0 :::80                   :::*                    LISTEN     
udp        0      0 0.0.0.0:12345           0.0.0.0:*                          
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 0.0.0.0:44291           0.0.0.0:*                          
udp        0      0 127.0.0.1:323           0.0.0.0:*                          
udp        0      0 0.0.0.0:10000           0.0.0.0:*                          
udp6       0      0 ::1:323                 :::*                               
```

## RPM安装服务和源码包安装服务的区别

它们的主要区别就是安装位置不同
- 源码包安装在指定位置，一般是/usr/local/，删除直接把目录干掉即可，不会产生任何垃圾文件。
- RPM包安装在默认的位置中，如配置相关在/etc, 启动脚本/etc/rc.d/init.d，RPM包删除必须加`-e`选项（把所有安装的文件干掉）。

# RPM包安装服务的管理

## 独立服务的管理

1. RPM包安装服务的位置
RPM安装服务和源码包安装服务的区别就是安装位置的不同。源码包安装在指定位置，一般是/usr/local/；RPM包安装在默认位置中，一般如下（注意是通用的，也有特殊）：
   - /etc/init.d：启动脚本位置，或者/etc/rc.d/init.d下，两者是软连接关系。
   - /etc/sysconfig/：初始化环境配置文件位置
   - /etc/：配置文件位置
   - /etc/xinetd.conf：xinetd配置文件
   - /etc/xinetd.d/：基于xinetd服务的启动脚本
   - /var/lib/：服务产生的数据放在这里
   - /var/log/：日志

2. 独立服务的启动
   - /etc/init.d/独立服务名 start|stop|status|restart|
   - service 独立服务名 start|stop|status|restart|，service是redhat专门准备的命令，`service --status-all`，查看所有
   - systemd启动：systemctl start|stop|status|restart 独立服务名

3. 独立服务的自启动
   - 自启动方法1：`chkconfig --list`，`chkconfig --level httpd on`，centos6可以用chkconfig，但是7就不行了
     ```bash
     [root@core-pods-3 ~]# systemctl list-unit-files  # centos7的systemd
     [root@core-pods-3 ~]# systemctl list-dependencies httpd
     ```
   - 自启动方法2：修改/etc/rc.d/rc.local，这个文件在系统启动后，会在在输入用户名密码之前会执行这个文件里命令
     ```bash
     [root@core-pods-3 ~]# vi /etc/rc.d/rc.local
     ```
   - 自启动方法3：使用ntsysv命令管理自启动
   - 自启动方法4：`systemctl enable httpd`，centos7

## 基于xinetd服务的管理

xinetd，超级守护进程服务，管理各个独立服务，目前基于xinetd的服务越来越少了。只有telnet服务（不太安全）和sync的网络备份服务，telnet现在远程管理也淘汰了，都在使用ssh。telnet默认端口23，开启后可以使用`netstat -tlun`查看。

### 安装xinetd与telnet

```bash
[root@core-pods-3 ~]# yum -y install xinetd
[root@core-pods-3 ~]# yum -y install telnet-server
[root@core-pods-3 ~]# chkconfig --list  # 教程里使用这个命令可以看到telnet，但是我的看不到
```

### xinetd服务的启动

命令是`vi /etc/xinetd.d/telnet`（因为我是centos7，所以没有这个目录，似乎centos7的telnet不再属于xinetd服务了），改过后重启服务`service xinetd restart`。

### xinetd服务的自启动

两种方式：`chkconfig telnet on` 或者 使用ntsysv图形界面管理（这个是红帽专有）。基于xinetd服务的服务启动和自启动区分不那么严格，有串通，就是有时候你改了自启动，会影响启动。

# 源码包安装服务的管理

1. 源码包安装服务的启动

   使用绝对路径，调用启动脚本来启动。不同的源码包的启动脚本不同。可以查看源码包的安装说明，查看启动脚本的方法。eg：`/usr/local/apache2/bin/apachectl start|stop`

   ```bash
   [root@core-pods-3 httpd-2.4.54]# pwd
   /usr/local/httpd/httpd-2.4.54
   [root@core-pods-3 httpd-2.4.54]# cat INSTALL | grep apachectl
        $ PREFIX/bin/apachectl start
   ```

2. 源码包服务的自启动

   修改/etc/rc.d/rc.local，`vi /etc/rc.d/rc.local`，加入`/usr/local/apache2/bin/apachectl start`

3. 让源码包服务被服务管理命令识别

   让源码包的apache服务能被service命令管理启动，只需要做个软连接即可：`ln -s /usr/local/apache2/bin/apachectl /etc/init.d/apache`，现在直接执行`service apache start`即可。不推荐这样做，主要是为了区分源码包和RPM包，容易混淆。

   如果让源码包的apache服务能被chkconfig与ntsysv命令管理自启动，需要`vi /etc/init.d/apache`，指定httpd脚本可以被chkconfig命令管理。
   ```
   格式是：
   chkconfig: 运行级别 启动顺序 关闭顺序   # 如'chkconfig: 25 86 76', 启动顺序 关闭顺序不能和现有的服务启动顺序 关闭顺序相同
   description: source package apache    # 这里内容随意
   ```

   查看启动顺序、关闭顺序

   ```bash
   [root@core-pods-3 httpd-2.4.54]# ll /etc/rc*     #  查看启动顺序、关闭顺序
   [root@core-pods-3 httpd-2.4.54]# ll /etc/rc3.d/   #  查看启动顺序、关闭顺序
   [root@core-pods-3 httpd-2.4.54]# chkconfig --add apache
   [root@core-pods-3 httpd-2.4.54]# chkconfig --list | grep apache
   [root@core-pods-3 httpd-2.4.54]# chkconfig apache on
   [root@core-pods-3 httpd-2.4.54]# chkconfig --list | grep apache
   ```
