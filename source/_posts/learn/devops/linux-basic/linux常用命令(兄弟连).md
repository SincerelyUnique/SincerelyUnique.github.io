---
title: linux常用命令（兄弟连）
date: 2022-07-15 14:34:43
tags:
categories:
- [学习, DevOPS, 兄弟连Linux学习]
valine:
  placeholder: ""
---

# 文件搜索命令
1. find：文件搜索，生产环境慎用，-iname忽略大小写，find ./ -name xxxx
2. locate：文件搜索，updatedb，/tmp目录不收录
3. whereis：可执行文件搜索, 查配置文件，帮助文档，可执行文件路径， 1-命令的帮助，5-胚子和文件的帮助，whereis passwd首先显示命令的帮助
4. which：可执行文件搜索
5. grep：文件内容搜索，类似还有cat，tac（逆转显示），more，less（区别于more，可以往回翻页）



# 帮助命令
查看命令作用，选项；

查看配置文件name，存放信息，文件格式

man services如果报No manual entry for services，执行
```bash
$ man ls

$ yum install -y man-pages
$ yum install -y man-db
$ mandb  # update the mandb database
$ man services

$ man passwd
$ whereis passwd
$ man 5 passwd  # 看passwd 配置文件的帮助文档，默认是看1-命令的帮助文档

$ whatis ls  # 命令的简短介绍信息
$ whatis ifconfig

$ apropos services
$ apropos /etc/inittab  # apropos + 配置文件，不要加绝对路径，去掉/etc/

$ touch --help
$ date
$ man date
$ date 031410272014.18
$ info ls  # 和man差不多，早期linux没有这个命令

$ help umask  # 获得shell内置命令的帮助信息，
$ which cd  # shell内置命令，man不到，bash builtins， 需要用help cd
$ which umask  # shell内置命令，man不到，bash builtins，需要用help umask，umask指定默认文件权限
$ help if
$ help case
$ help while
```
```
# 常见端口
telnet 23
ftp 21
http 80
https 443
ssh 22
```



# 用户管理命令
（1）useradd: 需root权限，添加用户
```bash
[root@core-pods-3 tmp]# useradd yangmi
```

（2）passwd：设置密码, root和yangmi可操作，root用户可以赋予某个user简单密码，但是yangmi自己不能，需符合复杂度要求
```bash
[root@core-pods-3 tmp]# passwd yangmi
```

（3）who：查看当前登录用户名，登录的终端（tty-本地终端，pts远程终端），登陆时间（登录主机地址）
```bash
[root@core-pods-3 tmp]# who
[root@core-pods-3 tmp]# whoami
```

（4）w：比who获得信息更多，up-linux连续运行时间，load average（过去1分钟，5分钟，15分钟负载数），idle用户登陆过来空闲多久了，pcpu-当前用户登陆过来占用的cpu时间， what-这个登录用户正在做什么
```bash
[root@core-pods-3 tmp]# w
[root@core-pods-3 tmp]# uptime
```



# 压缩解压命令
绝大多数病毒很难感染压缩文件，

（1）gzip：压缩格式.gz，压缩后源文件不见
```bash
[root@core-pods-3 tmp]# pwd
/tmp
[root@core-pods-3 tmp]# rm -rf *
[root@core-pods-3 tmp]# ls
[root@core-pods-3 tmp]# touch boduo
[root@core-pods-3 tmp]# gzip boduo
[root@core-pods-3 tmp]# ls
boduo.gz
[root@core-pods-3 tmp]# rm boduo.gz 
[root@core-pods-3 tmp]# cp /etc/services /tmp/boduo
[root@core-pods-3 tmp]# ls
boduo
[root@core-pods-3 tmp]# gzip boduo
[root@core-pods-3 tmp]# ls
boduo.gz
[root@core-pods-3 tmp]# 
```

（2）gunzip：解压缩, gunzip(gzip -d), 只能压缩文件
```bash
# 压缩 & 解压缩
[root@core-pods-3 tmp]# gzip boduo 
[root@core-pods-3 tmp]# ls -l
total 136
-rw-r--r-- 1 root root 136085 Jul 15 21:49 boduo.gz
[root@core-pods-3 tmp]# gunzip boduo.gz 
[root@core-pods-3 tmp]# ls -l
total 656
-rw-r--r-- 1 root root 670293 Jul 15 21:49 boduo
[root@core-pods-3 tmp]# gzip boduo 
[root@core-pods-3 tmp]# ls -l
total 136
-rw-r--r-- 1 root root 136085 Jul 15 21:49 boduo.gz
[root@core-pods-3 tmp]# gzip -d boduo.gz 
[root@core-pods-3 tmp]# ls -l
total 656
-rw-r--r-- 1 root root 670293 Jul 15 21:49 boduo

# gzip不可压缩目录
[root@core-pods-3 tmp]# pwd
/tmp
[root@core-pods-3 tmp]# ls
[root@core-pods-3 tmp]# mkdir japan
[root@core-pods-3 tmp]# mkdir japan/boduo japan/cangjing japan/longze
[root@core-pods-3 tmp]# ls
japan
[root@core-pods-3 tmp]# gzip japan/
gzip: japan/ is a directory -- ignored
[root@core-pods-3 tmp]# 
```

（3）tar：打包+压缩, 先打包再压缩，可打包目录，-c创建压缩，-x解包，-v显示详细信息，-f指定解压文件，-z解压缩
```bash
# 压缩
[root@core-pods-3 tmp]# tar -cvf japan.tar japan
japan/
japan/cangjing/
japan/longze/
japan/boduo/
[root@core-pods-3 tmp]# ls
japan  japan.tar
[root@core-pods-3 tmp]# gzip japan.tar 
[root@core-pods-3 tmp]# ls
japan  japan.tar.gz
[root@core-pods-3 tmp]# 

# 压缩（一条命令）
[root@core-pods-3 tmp]# pwd
/tmp
[root@core-pods-3 tmp]# ls
japan
[root@core-pods-3 tmp]# tar -zcf japan.tar.gz japan
[root@core-pods-3 tmp]# ls
japan  japan.tar.gz
[root@core-pods-3 tmp]# 

# 解压缩
[root@core-pods-3 tmp]# ls
japan  japan.tar.gz
[root@core-pods-3 tmp]# rm -rf japan
[root@core-pods-3 tmp]# ls
japan.tar.gz
[root@core-pods-3 tmp]# tar -zxvf japan.tar.gz 
japan/
japan/cangjing/
japan/longze/
japan/boduo/
[root@core-pods-3 tmp]# ls
japan  japan.tar.gz
[root@core-pods-3 tmp]# 
```

（4）zip：windows、linux都支持的压缩格式, 能保留源文件， -r压缩目录
```bash
# 压缩文件和目录
[root@core-pods-3 tmp]# ls
boduo  japan
[root@core-pods-3 tmp]# zip boduo.zip boduo
  adding: boduo (stored 0%)
[root@core-pods-3 tmp]# ls
boduo  boduo.zip  japan
[root@core-pods-3 tmp]# zip -r japan.zip japan
  adding: japan/ (stored 0%)
  adding: japan/cangjing/ (stored 0%)
  adding: japan/longze/ (stored 0%)
  adding: japan/boduo/ (stored 0%)
[root@core-pods-3 tmp]# ls
boduo  boduo.zip  japan  japan.zip
[root@core-pods-3 tmp]# 

# 解压缩
[root@core-pods-3 tmp]# ls
boduo.zip  japan.zip
[root@core-pods-3 tmp]# unzip boduo.zip 
Archive:  boduo.zip
 extracting: boduo                   
[root@core-pods-3 tmp]# unzip japan.zip 
Archive:  japan.zip
   creating: japan/
   creating: japan/cangjing/
   creating: japan/longze/
   creating: japan/boduo/
[root@core-pods-3 tmp]# ls
boduo  boduo.zip  japan  japan.zip
[root@core-pods-3 tmp]# 
```

（5）bzip2：gzip升级版本， -k产生压缩包并保留源文件，压缩比很高，推荐压缩大文件
```bash
# 压缩
[root@core-pods-3 tmp]# ls
boduo  japan
[root@core-pods-3 tmp]# bzip2 -k boduo
[root@core-pods-3 tmp]# ls
boduo  boduo.bz2  japan
[root@core-pods-3 tmp]# tar -cjf japan.tar.bz2 japan
[root@core-pods-3 tmp]# ls
boduo  boduo.bz2  japan  japan.tar.bz2
[root@core-pods-3 tmp]# 

# 解压缩
[root@core-pods-3 tmp]# ls
boduo.bz2  japan.tar.bz2
[root@core-pods-3 tmp]# bunzip2 -k boduo.bz2 
[root@core-pods-3 tmp]# ls
boduo  boduo.bz2  japan.tar.bz2
[root@core-pods-3 tmp]# tar -xjf japan.tar.bz2 
[root@core-pods-3 tmp]# ls
boduo  boduo.bz2  japan  japan.tar.bz2
[root@core-pods-3 tmp]# 
```

# 网络命令
（1）write：给在线用户发信息，即时通信，ctrl+d保存结束
```bash
# root用户终端 发送 信息
[root@core-pods-3 tmp]# write linzhiling
Hello linzhiling~
heihei~
[root@core-pods-3 tmp]# 

# linzhiling用户终端 收到 信息
[linzhiling@core-pods-3 ~]$ 
Message from root@core-pods-3.localdomain on pts/1 at 22:29 ...
Hello linzhiling~
heihei~
EOF
```

（2）wall：发广播消息, wall是write all缩写， `wall [message]`
```bash
# root用户发广播消息
[root@core-pods-3 tmp]# wall linzhiling play basketball

Broadcast message from root@core-pods-3.localdomain (pts/1) (Fri Jul 15 22:33:39 2022):

linzhiling play basketball
[root@core-pods-3 tmp]# 

# 其他在线用户收到
[linzhiling@core-pods-3 ~]$ 
Broadcast message from root@core-pods-3.localdomain (pts/1) (Fri Jul 15 22:33:39 2022):

linzhiling play basketball
```

（3）ping：-c指定发送次数，发送icmp请求包，看回应，测试网络连通性，`ping ip`，重点看packet loss丢包率
```bash
[root@core-pods-3 ~]# ping 140.82.112.4
PING 140.82.112.4 (140.82.112.4) 56(84) bytes of data.
64 bytes from 140.82.112.4: icmp_seq=1 ttl=54 time=54.9 ms
64 bytes from 140.82.112.4: icmp_seq=2 ttl=54 time=54.9 ms
64 bytes from 140.82.112.4: icmp_seq=3 ttl=54 time=54.8 ms
64 bytes from 140.82.112.4: icmp_seq=4 ttl=54 time=54.8 ms
^C
--- 140.82.112.4 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 54.876/54.903/54.926/0.235 ms
[root@core-pods-3 ~]# ping -c 3 140.82.112.4
PING 140.82.112.4 (140.82.112.4) 56(84) bytes of data.
64 bytes from 140.82.112.4: icmp_seq=1 ttl=54 time=54.8 ms
64 bytes from 140.82.112.4: icmp_seq=2 ttl=54 time=54.9 ms
64 bytes from 140.82.112.4: icmp_seq=3 ttl=54 time=55.0 ms

--- 140.82.112.4 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 54.883/54.957/55.037/0.201 ms
[root@core-pods-3 ~]# ping -c 2 www.github.com
PING github.com (192.30.255.112) 56(84) bytes of data.
64 bytes from lb-192-30-255-112-sea.github.com (192.30.255.112): icmp_seq=1 ttl=55 time=33.2 ms
64 bytes from lb-192-30-255-112-sea.github.com (192.30.255.112): icmp_seq=2 ttl=55 time=33.3 ms

--- github.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 33.206/33.256/33.307/0.189 ms
[root@core-pods-3 ~]# 
```

（4）ifconfig：查看当前的网络状态,网卡信息，eth0本地真实网卡，lo回环网卡-本机通信测试的（通常是127.0.0.1），MAC地址是固化到网卡上制度存储器上
```bash
[root@core-pods-3 ~]# ifconfig
eth0: xxxx
eth1: xxxx
lo: xxxx
[root@core-pods-3 ~]# ifconfig eth0 192.168.8.250  # 临时修改ip地址，局域网
```

（5）mail：发送电子邮件，不是用邮件服务器，用的是内存，网可以不通，mail root，如commaind not found，yum install mailx
```bash
[root@core-pods-3 ~]# mail root  # ctrl + D 发送
Subject: Test
Hello Nihao~
EOT
[root@core-pods-3 ~]# 

[root@core-pods-3 ~]# mail root
Subject: Test
Hello Nihao~
EOT
[root@core-pods-3 ~]# mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/root": 2 messages 2 new
>N  1 root                  Sun Jul 17 22:33  18/618   "test"
 N  2 root                  Sun Jul 17 22:35  18/619   "Test"
& 2
Message  2:
From root@core-pods-3.localdomain  Sun Jul 17 22:35:01 2022
Return-Path: <root@core-pods-3.localdomain>
X-Original-To: root
Delivered-To: root@core-pods-3.localdomain
Date: Sun, 17 Jul 2022 22:35:01 -0400
To: root@core-pods-3.localdomain
Subject: Test
User-Agent: Heirloom mailx 12.5 7/5/10
Content-Type: text/plain; charset=us-ascii
From: root@core-pods-3.localdomain (root)
Status: R

Hello Nihao~

& help
               mail commands
type <message list>             type messages
next                            goto and type next message
from <message list>             give head lines of messages
headers                         print out active message headers
delete <message list>           delete messages
undelete <message list>         undelete messages
save <message list> folder      append messages to folder and mark as saved
copy <message list> folder      append messages to folder without marking them
write <message list> file       append message texts to file, save attachments
preserve <message list>         keep incoming messages in mailbox even if saved
Reply <message list>            reply to message senders
reply <message list>            reply to message senders and all recipients
mail addresses                  mail to specific recipients
file folder                     change to another folder
quit                            quit and apply changes to folder
xit                             quit and discard changes made to folder
!                               shell escape
cd <directory>                  chdir to directory or home if none given
list                            list names of all available commands

A <message list> consists of integers, ranges of same, or other criteria
separated by spaces.  If omitted, mail uses the last message typed.
& h
 N  1 root                  Sun Jul 17 22:33  18/618   "test"
>   2 root                  Sun Jul 17 22:35  18/619   "Test"
& d 2
& h
>N  1 root                  Sun Jul 17 22:33  18/618   "test"
& q
Held 1 message in /var/spool/mail/root
You have mail in /var/spool/mail/root
[root@core-pods-3 ~]# 
```

（6）last：列出目前与过去系统用户登录信息，系统启动信息
```bash
[root@core-pods-3 ~]# last
root     pts/1        64.104.44.101    Sun Jul 17 22:34   still logged in   
root     pts/1        64.104.44.101    Sun Jul 17 22:21 - 22:33  (00:12)    
linzhili pts/2        116.149.199.7    Fri Jul 15 22:29 - 02:03  (03:33)    
root     pts/2        116.149.199.7    Fri Jul 15 22:27 - 22:28  (00:00)    
root     pts/1        116.149.199.7    Fri Jul 15 21:55 - 02:03  (04:07)    
root     pts/1        116.149.199.7    Fri Jul 15 21:45 - 21:55  (00:10)    
root     pts/1        64.104.44.105    Thu Jul 14 23:56 - 05:27  (05:30)    
root     pts/1        64.104.44.111    Thu Jul 14 11:34 - 12:05  (00:31)    
root     pts/1        116.149.199.174  Thu Jul 14 11:09 - 11:14  (00:05)    
root     pts/1        64.104.44.99     Thu Jul  7 04:28 - 05:24  (00:55)    
root     pts/1        64.104.44.99     Thu Jul  7 02:59 - 04:28  (01:28)    
root     pts/2        116.149.207.92   Wed Jul  6 09:06 - 09:36  (00:29)    
root     pts/1        116.149.207.92   Wed Jul  6 09:05 - 09:06  (00:01)    
root     pts/1        64.104.44.102    Tue Jul  5 05:19 - 05:22  (00:03)    
root     pts/0        64.104.44.107    Fri Jul  1 00:12 - 05:23  (05:10)    
root     pts/0        64.104.44.107    Fri Jul  1 00:10 - 00:11  (00:00)    
reboot   system boot  4.10.4-1.el7.elr Thu Jun 30 05:22 - 22:40 (17+17:17)  

wtmp begins Thu Jun 30 05:22:22 2022
[root@core-pods-3 ~]# 

[root@core-pods-3 ~]# lastlog
Username         Port     From             Latest
root             pts/1    64.104.44.101    Sun Jul 17 22:34:30 -0400 2022
bin                                        **Never logged in**
daemon                                     **Never logged in**
adm                                        **Never logged in**
lp                                         **Never logged in**
sync                                       **Never logged in**
shutdown                                   **Never logged in**
halt                                       **Never logged in**
mail                                       **Never logged in**
operator                                   **Never logged in**
games                                      **Never logged in**
ftp                                        **Never logged in**
nobody                                     **Never logged in**
systemd-bus-proxy                           **Never logged in**
systemd-network                            **Never logged in**
dbus                                       **Never logged in**
polkitd                                    **Never logged in**
tss                                        **Never logged in**
sshd                                       **Never logged in**
postfix                                    **Never logged in**
chrony                                     **Never logged in**
nginx                                      **Never logged in**
linzhiling       pts/2    116.149.199.7    Fri Jul 15 22:29:42 -0400 2022
[root@core-pods-3 ~]# 

[root@core-pods-3 ~]# cat /etc/passwd|grep linzhiling
linzhiling:x:1000:1000::/home/linzhiling:/bin/bash
[root@core-pods-3 ~]# lastlog -u 1000
Username         Port     From             Latest
linzhiling       pts/2    116.149.199.7    Fri Jul 15 22:29:42 -0400 2022
[root@core-pods-3 ~]# 
```

(7)traceroute：显示数据包到主机间的路径信息，路由追踪
```bash
[root@core-pods-3 ~]# traceroute 140.82.112.4
traceroute to 140.82.112.4 (140.82.112.4), 30 hops max, 60 byte packets
 1  * * *
 2  lax1-5-1.it7.net (192.161.172.5)  0.939 ms  0.932 ms  0.905 ms
 3  lax1-fatpipe-1.it7.net (69.12.70.234)  0.303 ms lax1-fatpipe-1.it7.net (69.12.70.232)  0.272 ms lax1-fatpipe-1.it7.net (69.12.70.234)  0.375 ms
 4  et-8-1-6.mcs1.lax112.us.eth.zayo.com.zip.zayo.com (208.185.217.229)  0.501 ms  0.502 ms las-b24-link.ip.twelve99.net (213.248.71.105)  0.672 ms
 5  rest-bb1-link.ip.twelve99.net (62.115.114.87)  59.724 ms ae1.cs2.lax112.us.eth.zayo.com (64.125.28.236)  57.966 ms ash-bb2-link.ip.twelve99.net (62.115.121.221)  55.369 ms
 6  * ash-b2-link.ip.twelve99.net (62.115.123.123)  55.803 ms *
 7  * * github-ic345015-ash-b2.ip.twelve99-cust.net (62.115.175.99)  57.507 ms
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *
[root@core-pods-3 ~]# traceroute www.sina.com.cn
traceroute to www.sina.com.cn (8.45.176.232), 30 hops max, 60 byte packets
 1  * * *
 2  lax1-5-1.it7.net (192.161.172.5)  0.861 ms  0.882 ms  0.898 ms
 3  lax1-fatpipe-1.it7.net (69.12.70.232)  0.341 ms  0.314 ms lax1-fatpipe-1.it7.net (69.12.70.234)  0.227 ms
 4  las-b24-link.ip.twelve99.net (213.248.71.105)  0.546 ms 63.218.212.169 (63.218.212.169)  0.655 ms  0.593 ms
 5  * * Bundle-Ether4.br05.lax05.pccwbtn.net (63.223.43.198)  0.841 ms
 6  * * *
 7  * * *
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *
[root@core-pods-3 ~]# 
```

（8）netstat：查询网络状态,显示网络相关信息。-t查询TCP协议，-u查询UDP协议，-l监听，-r路由，-n显示ip和端口号，-a指all
```bash
[root@core-pods-3 ~]# netstat -tlun
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:29108           0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:12345           0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN     
tcp6       0      0 :::29108                :::*                    LISTEN     
tcp6       0      0 ::1:25                  :::*                    LISTEN     
tcp6       0      0 :::443                  :::*                    LISTEN     
tcp6       0      0 :::80                   :::*                    LISTEN     
udp        0      0 0.0.0.0:12345           0.0.0.0:*                          
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 0.0.0.0:44291           0.0.0.0:*                          
udp        0      0 127.0.0.1:323           0.0.0.0:*                          
udp        0      0 0.0.0.0:52960           0.0.0.0:*                          
udp        0      0 0.0.0.0:10096           0.0.0.0:*                          
udp6       0      0 :::55543                :::*                               
udp6       0      0 ::1:323                 :::*                               
udp6       0      0 :::52960                :::*                               
[root@core-pods-3 ~]# netstat -an
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:29108           0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:12345           0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN     
tcp        0      0 104.225.146.204:443     3.219.234.229:28645     SYN_RECV   
tcp        0    232 104.225.146.204:29108   64.104.44.101:3100      ESTABLISHED
tcp6       0      0 :::29108                :::*                    LISTEN     
tcp6       0      0 ::1:25                  :::*                    LISTEN     
tcp6       0      0 :::443                  :::*                    LISTEN     
tcp6       0      0 :::80                   :::*                    LISTEN     
udp        0      0 0.0.0.0:12345           0.0.0.0:*                          
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 0.0.0.0:44291           0.0.0.0:*                          
udp        0      0 127.0.0.1:323           0.0.0.0:*                          
udp        0      0 0.0.0.0:52960           0.0.0.0:*                          
udp        0      0 0.0.0.0:10096           0.0.0.0:*                          
udp6       0      0 :::55543                :::*                               
udp6       0      0 ::1:323                 :::*                               
udp6       0      0 :::52960                :::*                               
Active UNIX domain sockets (servers and established)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  2      [ ]         DGRAM                    9004     /run/systemd/notify
unix  2      [ ]         DGRAM                    9006     /run/systemd/cgroups-agent
unix  2      [ ACC ]     STREAM     LISTENING     9017     /run/systemd/journal/stdout
unix  5      [ ]         DGRAM                    9020     /run/systemd/journal/socket
unix  16     [ ]         DGRAM                    9022     /dev/log
unix  2      [ ACC ]     STREAM     LISTENING     13139    /var/run/NetworkManager/private-dhcp
unix  2      [ ACC ]     STREAM     LISTENING     12643    /var/run/dbus/system_bus_socket
unix  2      [ ACC ]     STREAM     LISTENING     130919   /var/run/acpid.socket
unix  2      [ ACC ]     STREAM     LISTENING     128816   public/pickup
unix  2      [ ACC ]     STREAM     LISTENING     128820   public/cleanup
unix  2      [ ACC ]     STREAM     LISTENING     128823   public/qmgr
unix  2      [ ACC ]     STREAM     LISTENING     128827   private/tlsmgr
unix  2      [ ACC ]     STREAM     LISTENING     128830   private/rewrite
unix  2      [ ACC ]     STREAM     LISTENING     128833   private/bounce
unix  2      [ ACC ]     STREAM     LISTENING     128836   private/defer
unix  2      [ ACC ]     STREAM     LISTENING     128839   private/trace
unix  2      [ ACC ]     STREAM     LISTENING     128842   private/verify
unix  2      [ ACC ]     STREAM     LISTENING     128845   public/flush
unix  2      [ ACC ]     STREAM     LISTENING     128848   private/proxymap
unix  2      [ ACC ]     STREAM     LISTENING     128851   private/proxywrite
unix  2      [ ACC ]     STREAM     LISTENING     128854   private/smtp
unix  2      [ ACC ]     STREAM     LISTENING     128857   private/relay
unix  2      [ ACC ]     STREAM     LISTENING     128860   public/showq
unix  2      [ ACC ]     STREAM     LISTENING     128863   private/error
unix  2      [ ACC ]     STREAM     LISTENING     128866   private/retry
unix  2      [ ACC ]     STREAM     LISTENING     128869   private/discard
unix  2      [ ACC ]     STREAM     LISTENING     128872   private/local
unix  2      [ ACC ]     STREAM     LISTENING     128875   private/virtual
unix  2      [ ACC ]     STREAM     LISTENING     128878   private/lmtp
unix  2      [ ACC ]     STREAM     LISTENING     128881   private/anvil
unix  2      [ ACC ]     STREAM     LISTENING     128884   private/scache
unix  2      [ ACC ]     SEQPACKET  LISTENING     123554   /run/udev/control
unix  2      [ ]         DGRAM                    9934     /run/systemd/shutdownd
unix  2      [ ACC ]     STREAM     LISTENING     131335   @/sys/entropy/haveged
unix  2      [ ]         DGRAM                    130257   /var/run/chrony/chronyd.sock
unix  2      [ ACC ]     STREAM     LISTENING     124122   /run/systemd/private
unix  3      [ ]         STREAM     CONNECTED     129639   /run/systemd/journal/stdout
unix  2      [ ]         DGRAM                    129169   
unix  3      [ ]         STREAM     CONNECTED     128886   
unix  3      [ ]         STREAM     CONNECTED     129638   
unix  2      [ ]         DGRAM                    129185   
unix  3      [ ]         STREAM     CONNECTED     128862   
unix  3      [ ]         STREAM     CONNECTED     128861   
unix  3      [ ]         STREAM     CONNECTED     128882   
unix  3      [ ]         STREAM     CONNECTED     128885   
unix  3      [ ]         STREAM     CONNECTED     131036   /run/systemd/journal/stdout
unix  2      [ ]         DGRAM                    129232   
unix  3      [ ]         STREAM     CONNECTED     128883   
unix  3      [ ]         STREAM     CONNECTED     12701    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     13624    
unix  3      [ ]         STREAM     CONNECTED     128864   
unix  2      [ ]         DGRAM                    275950   
unix  2      [ ]         DGRAM                    129443   
unix  3      [ ]         STREAM     CONNECTED     128865   
unix  3      [ ]         STREAM     CONNECTED     128814   
unix  3      [ ]         STREAM     CONNECTED     128403   
unix  3      [ ]         STREAM     CONNECTED     129005   
unix  3      [ ]         STREAM     CONNECTED     128822   
unix  2      [ ]         DGRAM                    123559   
unix  3      [ ]         STREAM     CONNECTED     127656   /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     128817   
unix  3      [ ]         STREAM     CONNECTED     178619   
unix  2      [ ]         DGRAM                    12863    
unix  3      [ ]         STREAM     CONNECTED     12869    
unix  3      [ ]         STREAM     CONNECTED     131122   /var/run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     130660   /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     12870    /var/run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     130698   
unix  3      [ ]         STREAM     CONNECTED     130001   
unix  3      [ ]         STREAM     CONNECTED     178618   
unix  3      [ ]         STREAM     CONNECTED     129007   /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     178620   
unix  3      [ ]         STREAM     CONNECTED     128815   
unix  3      [ ]         DGRAM                    123574   
unix  2      [ ]         DGRAM                    130915   
unix  3      [ ]         STREAM     CONNECTED     12918    
unix  3      [ ]         STREAM     CONNECTED     131035   
unix  2      [ ]         DGRAM                    130070   
unix  3      [ ]         STREAM     CONNECTED     128821   
unix  3      [ ]         DGRAM                    123573   
unix  2      [ ]         DGRAM                    12901    
unix  3      [ ]         STREAM     CONNECTED     128818   
unix  3      [ ]         STREAM     CONNECTED     14129    /var/run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     128825   
unix  3      [ ]         STREAM     CONNECTED     128850   
unix  3      [ ]         STREAM     CONNECTED     128840   
unix  3      [ ]         STREAM     CONNECTED     128834   
unix  3      [ ]         STREAM     CONNECTED     130523   /var/run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     128856   
unix  3      [ ]         STREAM     CONNECTED     128828   
unix  3      [ ]         STREAM     CONNECTED     178621   
unix  2      [ ]         DGRAM                    129838   
unix  3      [ ]         STREAM     CONNECTED     13770    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     128867   
unix  3      [ ]         STREAM     CONNECTED     128832   
unix  3      [ ]         STREAM     CONNECTED     128852   
unix  2      [ ]         DGRAM                    12939    
unix  3      [ ]         STREAM     CONNECTED     130072   
unix  3      [ ]         STREAM     CONNECTED     128841   
unix  3      [ ]         STREAM     CONNECTED     128829   
unix  3      [ ]         STREAM     CONNECTED     128855   
unix  3      [ ]         STREAM     CONNECTED     22988    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     128837   
unix  3      [ ]         STREAM     CONNECTED     22985    
unix  3      [ ]         STREAM     CONNECTED     128846   
unix  3      [ ]         STREAM     CONNECTED     128868   
unix  3      [ ]         STREAM     CONNECTED     14228    /var/run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     128859   
unix  3      [ ]         STREAM     CONNECTED     128838   
unix  3      [ ]         STREAM     CONNECTED     12952    
unix  3      [ ]         STREAM     CONNECTED     128831   
unix  3      [ ]         STREAM     CONNECTED     22984    
unix  2      [ ]         DGRAM                    130250   
unix  3      [ ]         STREAM     CONNECTED     128824   
unix  3      [ ]         STREAM     CONNECTED     128849   
unix  3      [ ]         STREAM     CONNECTED     128835   
unix  3      [ ]         STREAM     CONNECTED     13769    
unix  3      [ ]         STREAM     CONNECTED     129836   
unix  3      [ ]         STREAM     CONNECTED     128847   
unix  3      [ ]         STREAM     CONNECTED     128858   
unix  3      [ ]         STREAM     CONNECTED     22987    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     128853   
unix  3      [ ]         STREAM     CONNECTED     12766    
unix  3      [ ]         STREAM     CONNECTED     13982    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     128877   
unix  3      [ ]         STREAM     CONNECTED     12722    
unix  3      [ ]         STREAM     CONNECTED     13981    
unix  3      [ ]         STREAM     CONNECTED     128843   
unix  3      [ ]         STREAM     CONNECTED     128874   
unix  3      [ ]         STREAM     CONNECTED     124121   
unix  3      [ ]         STREAM     CONNECTED     12768    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     130071   
unix  3      [ ]         STREAM     CONNECTED     128870   
unix  3      [ ]         STREAM     CONNECTED     128876   
unix  3      [ ]         STREAM     CONNECTED     123542   /var/run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     124184   
unix  3      [ ]         STREAM     CONNECTED     128880   
unix  2      [ ]         DGRAM                    276669   
unix  3      [ ]         STREAM     CONNECTED     128844   
unix  3      [ ]         STREAM     CONNECTED     128873   
unix  3      [ ]         STREAM     CONNECTED     128879   
unix  2      [ ]         DGRAM                    13189    
unix  2      [ ]         DGRAM                    161959   
unix  3      [ ]         STREAM     CONNECTED     128871   
unix  2      [ ]         DGRAM                    13188    
unix  3      [ ]         STREAM     CONNECTED     124186   /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     12721    
[root@core-pods-3 ~]# netstat -rn
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         104.225.144.1   0.0.0.0         UG        0 0          0 eth0
23.252.98.117   104.225.144.1   255.255.255.255 UGH       0 0          0 eth0
104.225.144.0   0.0.0.0         255.255.240.0   U         0 0          0 eth0
169.254.254.254 0.0.0.0         255.255.255.255 UH        0 0          0 eth1
[root@core-pods-3 ~]# 
```

（9）setup：配置网络，redhat专有，支持防火墙，配置ip地址(永久生效)，系统服务配置，DHCP-自动分配、自动获取服务，yum install setuptool
```bash
[root@core-pods-3 ~]# setup
[root@core-pods-3 ~]# service network restart
Restarting network (via systemctl):                        [  OK  ]
[root@core-pods-3 ~]# 
```

（10）mount：挂载，把设备连接到挂载点（理解为盘符，比如windows C，D，E盘）
```bash
[root@core-pods-3 ~]# mkdir /mnt/cdrom
[root@core-pods-3 ~]# mount -t iso9660 /dev/sr0 /mnt/cdrom/
[root@core-pods-3 ~]# unmount /dev/sr0   # 或者是umount
[root@core-pods-3 ~]# cd /mnt/cdrom/
[root@core-pods-3 cdrom]# ll
```

# 关机重启命令
（1）shutdown：-h关机（服务器一般不允许关机），-r重启，-c取消前一个关机命令。关机前先停服务，伤物理硬盘，多用户。
```bash
# 推荐命令
[root@core-pods-3 cdrom]# shutdown -h now
[root@core-pods-3 cdrom]# shutdown -h 20:30
[root@core-pods-3 cdrom]# shutdown -r now

# 其他关机命令
[root@core-pods-3 cdrom]# halt
[root@core-pods-3 cdrom]# poweroff
[root@core-pods-3 cdrom]# init 0

# 其他重启命令
[root@core-pods-3 cdrom]# reboot
[root@core-pods-3 cdrom]# init 6  # 系统运行级别
# 系统运行级别0~6：
# 0-关机
# 1-单用户（类似windows F8->安全模式-做修复），没有图形界面
# 2-不完全多用户，不包含NFS（linux、unix两个系统之间文件共享服务，不建议使用NFS做文件共享，安全风险）服务，也没有网络，没有图形界面
# 3-完全多用户，字符界面，没有图形界面
# 4-未分配
# 5-X11（代表linux图形界面），图形界面
# 6-重启
[root@core-pods-3 cdrom]# cat /etc/inittab   # 修改系统运行级别： id:3:initdefault:
[root@core-pods-3 cdrom]# runlevel  # 查看系统运行级别：
N 3
[root@core-pods-3 cdrom]# init 5
[root@core-pods-3 cdrom]# runlevel
3 5
[root@core-pods-3 cdrom]# init 3
[root@core-pods-3 cdrom]# runlevel
5 3
[root@core-pods-3 cdrom]# logout  # 退出
```


# Vim常用操作
早期版本叫Vi，没有菜单，只有命令。分为命令模式/插入模式/编辑模式。

(1) 常见操作
```bash
# vi filename进入命令模式
# - 命令模式下，输入i a o 进入插入模式（按ESC返回命令模式）
# - 命令模式下，输入: 进入编辑模式
[root@core-pods-3 ~]# vi test  

# 定位命令
# G           跳转到文档末尾
# nG          跳转到第n行，如：32G，到第32行
# gg          跳转到文档首行
# 0           光标移动到行首
# $           光标移动到行尾
# :set nu     显示行号
# :set nonu   取消行号
# :n          到第n行，如:32，到第32行

# 插入命令
# a           在当前光标所在字符后插入
# A           在当前光标所在行行尾插入
# i           在当前光标所在字符前插入
# I           在当前光标所在行行首插入
# o           在当前光标的下一行插入新行
# O           在当前光标的上一行插入新行

# 删除命令
# x           删除光标所在处字符
# nx          删除光标所在处后n个字符，包含光标所在处字符
# dd          删除光标所在的当前行
# dG          删除光标所在行 及 所在行到文件末尾 的所有内容
# D           删除光标所在行 到 行尾 的内容
# :n1,n2d     删除第n1行到n2行之间文件内容，包含n1和n2行

# 复制和剪切命令
# yy          复制当前行
# nyy         复制当前行以下n行
# dd          剪切当前行
# ndd         剪切当前行以下n行
# p，P        粘贴在当前光标所在行下方 或 上方

# 替换和取消命令
# r           取代光标所在处字符
# R           从光标所在处开始替换字符，按Esc结束
# u           取消上一步操作，撤销undo

# 搜索和搜索替换命令
# /string             搜索指定字符串，搜索时忽略大小写:set ic
# n                   搜索指定字符串的下一个出现位置
# :%s/old/new/g       全文替换指定字符串，（g不询问，c询问）
# :n1,n2s/old/new/g   在一定范围内替换指定字符串

# 保存和退出命令
# :w                  保存修改：write
# :w new_filename     另存为指定文件
# :wq                 保存并退出：write quit
# ZZ                  快捷键，保存修改并退出
# :q!                 不保存修改退出
# :wq!                保存修改并退出（文件所有者和root可使用）
```

（2）使用技巧
可以在用户根目录下.vimrc下定义一些快捷键或常用命令之类的信息，这样即便重启也是生效的
```bash
# 导入命令执行结果 :r ! 命令 
# :r /etc/issue      # 从光标处导入某个文件内容
# :!which ls         # 查看ls命令所在路径
# :r !date           # 导入当前日期

# 定义快捷键 :map 快捷键 触发命令
# :map ^P I#<Esc>    # 注意^P的颜色，这里不是shift+6，而是ctrl + V和ctrl + P, 定义快捷键后可以自动通过ctrl + p给某一行加注释
# :map ^B 0x         # 

# 注释连续行
# :n1,n2s/^/#/g      # :1,4s/^/#/g 将第一行到第四行注释掉，取消注释 :1,4s/^#//g
# :n1,n2s/^#//g
# :n1,n2s/^/\/\//g   # 插入“//”注释

# 替换
# :ab jalen jalchu@cisco.com  # 敲入jalen后会空格或回车，自动变成jalchu@cisco.com, 有点像定义宏
```
