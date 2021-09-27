---
title: 记一次semaphore的问题
date: 2020-11-26 13:53:42
tags:
- linux
- semaphore
categories:
---

```
CDC是我们上包的工具，平时我们会通过Jenkis打rpm包，然后再通过CDC刷这个包，
最近某台server经常遇到CDC刷包失败的问题，于是先自己查了一下CDC上包的log，
发现有个报错，错误信息是ERROR: monxpmp can not be started.，于是和CDC 
Team一起查了一下，最终原因是当前server的linux信号量满了，下面是检查步骤：
 
首先明确一下：monxpmp是需要用系统semaphore的，系统semaphore array满了
monxpmp就启动不了了
 
1.尝试在机器上尝试移除monxpmp信号并重启monxpmp
[root@prodhostname test]# for semid in `ipcs -s | grep monxpmp | awk '{print $2}'`; do ipcrm -s $semid; done
[root@prodhostname test]# service monxpmp start
INFO: alertsender has been running...
INFO: snmpd has been running...
INFO: monxpmpagt has been running...
ls: cannot access /opt/web/mon/log/monxpmphtp.log.*: No such file or directory
ERROR: monxpmp can not be started.
INFO: mondatapush is started.
Succeed to enable auto recover check.
[root@prodhostname test]# 
 
2.查看server信息
[root@prodhostname test]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.252.25.151    prodhostname.web.com    prodhostname
[root@prodhostname test]# cat /etc/hostname 
prodhostname.web.com
[root@prodhostname test]# 
 
3.查看信号量大小，可以看到信号量满了，系统默认提供为128（132>128是因为有表头）
[root@prodhostname test]# ipcs -s | wc -l
132
[root@prodhostname test]# cat /proc/sys/kernel/sem  （查看信号量设置方法1）
250	32000	32	128
[root@prodhostname test]# sysctl -a | grep "kernel.sem = " （查看信号量设置方法2）
kernel.sem = 250	32000	32	128
sysctl: reading key "net.ipv6.conf.all.stable_secret"
sysctl: reading key "net.ipv6.conf.default.stable_secret"
sysctl: reading key "net.ipv6.conf.ens192.stable_secret"
sysctl: reading key "net.ipv6.conf.lo.stable_secret"
[root@prodhostname test]# ipcs -ls  （查看信号量设置方法3）
 
------ Semaphore Limits --------
max number of arrays = 128
max semaphores per array = 250
max semaphores system wide = 32000
max ops per semop call = 32
semaphore max value = 32767
 
[root@prodhostname test]# ipcs -l  （查看信号量设置方法4）
 
------ Messages Limits --------
max queues system wide = 31764
max size of message (bytes) = 8192
default max size of queue (bytes) = 16384
 
------ Shared Memory Limits --------
max number of segments = 4096
max seg size (kbytes) = 18014398509465599
max total shared memory (kbytes) = 18014398442373116
min seg size (bytes) = 1
 
------ Semaphore Limits --------
max number of arrays = 128
max semaphores per array = 250
max semaphores system wide = 32000
max ops per semop call = 32
semaphore max value = 32767
 
[root@zsj2sat301 test]# ipcs -u （查看信号量使用状况）
 
------ Messages Status --------
allocated queues = 0
used headers = 0
used space = 0 bytes
 
------ Shared Memory Status --------
segments allocated 66
pages allocated 7926
pages resident  7926
pages swapped   0
Swap performance: 0 attempts	 0 successes
 
------ Semaphore Status --------
used arrays = 128
allocated semaphores = 128
 
[root@prodhostname test]#
 
4.解决方法一：找不到哪个用户资源申请的话，最简单的办法就是reboot
 
5.解决方法二：暴力全部释放
[root@prodhostname test]# for semid in `ipcs -s | awk '{print $2}'`; do ipcrm -s $semid; done
[root@prodhostname test]#
（释放了之后当时申请信号量的程序不确定会不会出问题，方法不是很好）
 
6.解决方法三：调高信号量数组，这里调为256
[root@prodhostname test]# sysctl -w kernel.sem="250 32000 32 256"
kernel.sem = 250 32000 32 256
[root@prodhostname test]# ipcs -s | wc -l
132
[root@prodhostname test]# 
 
7.启动monxpmp应用，启动成功
[root@prodhostname test]# service monxpmp start
INFO: alertsender has been running...
INFO: snmpd has been running...
INFO: monxpmpagt has been running...
ls: cannot access /opt/web/mon/log/monxpmphtp.log.*: No such file or directory
INFO: monxpmp is started.
INFO: mondatapush is started.
Succeed to enable auto recover check.
[root@prodhostname test]# 
 
8.查看信号量列表，发现monxpmp启动添加成功
[root@prodhostname test]# ipcs -s
 
------ Semaphore Arrays --------
key        semid      owner      perms      nsems     
0x00000000 3768320    root       600        1         
0x00000000 3801089    root       600        1         
0x00000000 3964930    root       600        1         
0x00000000 3997699    root       600        1         
0x00000000 2457604    root       600        1         
0x00000000 2490373    root       600        1         
0x00000000 3440646    root       600        1         
0x00000000 3473415    root       600        1         
0x00000000 3506184    root       600        1         
0x00000000 3538953    root       600        1         
0x00000000 3702794    root       600        1         
0x00000000 3735563    root       600        1         
0x00000000 4161548    root       600        1         
0x00000000 4194317    root       600        1         
0x00000000 4358158    root       600        1         
0x00000000 4390927    root       600        1         
0x00000000 4620304    root       600        1         
0x00000000 4653073    root       600        1         
0x00000000 4816914    root       600        1         
0x00000000 4849683    root       600        1         
0x00000000 4554772    root       600        1         
0x00000000 4587541    root       600        1         
0x00000000 5013526    root       600        1         
0x00000000 5046295    root       600        1         
0x00000000 5210136    root       600        1         
0x00000000 5242905    root       600        1         
0x00000000 5406746    root       600        1         
0x00000000 5439515    root       600        1         
0x00000000 5603356    root       600        1         
0x00000000 5636125    root       600        1         
0x00000000 5799966    root       600        1         
0x00000000 5832735    root       600        1         
0x00000000 6127648    root       600        1         
0x00000000 6160417    root       600        1         
0x00000000 6389794    root       600        1         
0x00000000 6422563    root       600        1         
0x00000000 5996580    root       600        1         
0x00000000 6029349    root       600        1         
0x00000000 6062118    root       600        1         
0x00000000 6094887    root       600        1         
0x00000000 6717480    root       600        1         
0x00000000 6750249    root       600        1         
0x00000000 6324266    root       600        1         
0x00000000 6357035    root       600        1         
0x00000000 6914092    root       600        1         
0x00000000 6946861    root       600        1         
0x00000000 6586414    root       600        1         
0x00000000 6619183    root       600        1         
0x00000000 6651952    root       600        1         
0x00000000 6684721    root       600        1         
0x00000000 7110706    root       600        1         
0x00000000 7143475    root       600        1         
0x00000000 7372852    root       600        1         
0x00000000 7405621    root       600        1         
0x00000000 7569462    root       600        1         
0x00000000 7602231    root       600        1         
0x00000000 7307320    root       600        1         
0x00000000 7340089    root       600        1         
0x00000000 8093754    root       600        1         
0x00000000 8126523    root       600        1         
0x00000000 8355900    root       600        1         
0x00000000 8388669    root       600        1         
0x00000000 7766078    root       600        1         
0x00000000 7798847    root       600        1         
0x00000000 7831616    root       600        1         
0x00000000 7864385    root       600        1         
0x00000000 7897154    root       600        1         
... ...
0x00000000 12812412   root       600        1         
0x00000000 12845181   root       600        1         
0x00000000 12943486   root       600        1         
0x00000000 12976255   root       600        1         
0x00000000 13140096   root       600        1         
0x00000000 13172865   root       600        1         
0x50038d9e 13205634   monxpmp    666        1         
0x52038d9e 13238403   monxpmp    666        1         
0x64038d9e 13271172   monxpmp    666        1         
0x4f038d9e 13303941   monxpmp    666        1
 
9.CDC尝试刷包，成功
 
 
参考：
https://zh.wikipedia.org/wiki/%E4%BF%A1%E5%8F%B7%E9%87%8F
https://baike.baidu.com/item/%E4%BF%A1%E5%8F%B7%E9%87%8F
https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/ipcs.html
https://en.wikipedia.org/wiki/Semaphore_(programming)
https://linoxide.com/linux-how-to/semaphore/
https://www.thegeekdiary.com/how-to-change-kernel-semaphore-limits-in-centos-rhel/
 
 
附录：
1.ipcs用法
ipcs -a  是默认的输出信息 打印出当前系统中所有的进程间通信方式的信息
ipcs -m  打印出使用共享内存进行进程间通信的信息
ipcs -q   打印出使用消息队列进行进程间通信的信息
ipcs -s  打印出使用信号进行进程间通信的信息
ipcs -t   输出信息的详细变化时间
ipcs -p  输出ipc方式的进程ID
ipcs -c  输出ipc方式的创建者/拥有者，输出ipc各种方式的在该系统下的限制条件信息
ipcs -u  输出当前系统下ipc各种方式的状态信息(共享内存，消息队列，信号)
 
2.ipcrm用法
ipcrm -M shmkey  移除用shmkey创建的共享内存段
ipcrm -m shmid    移除用shmid标识的共享内存段
ipcrm -Q msgkey  移除用msqkey创建的消息队列
ipcrm -q msqid  移除用msqid标识的消息队列
ipcrm -S semkey  移除用semkey创建的信号
ipcrm -s semid  移除用semid标识的信号
```
