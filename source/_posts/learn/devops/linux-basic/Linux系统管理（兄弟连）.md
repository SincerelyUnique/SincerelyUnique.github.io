---
title: Linux系统管理（兄弟连）
date: 2022-08-14 14:34:43
tags:
categories:
- [学习, DevOPS, 兄弟连Linux学习]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 进程管理

## 进程查看

### 进程简介
进程是正在执行的一个程序或命令，每个进程都是一个运行的实体，都有自己的地址空间，并占用一定的系统资源。每个程序或命令都至少会产生一个进程，比如ls命令就会产生一个进程，只是这个进程执行很快。

### 进程管理的作用

- 判断服务器健康状态（cpu、memory、disk）
- 查看系统中所有进程
- 杀死进程（最不常用，我们理应正常关闭进程，只有无法正常关闭时使用这种方法，不要总是kill）
- 服务器比较多，需要搭建监控服务器

### 查看系统中所有进程

```bash
[root@core-pods-3 ~]# ps aux      # 查看系统中所有进程，使用BSD操作系统格式
# 输出说明
# USER: 该进程是由哪个用户产生的，比如root用户产生的
# PID：进程的ID号，linux系统识别进程的唯一有效信息，pid=1是初始进程（centos6是/sbin/init,centos7是/usr/lib/systemd/systemd）
# %CPU：该进程占用cpu资源的百分比，占用越高，进程越耗费资源
# %MEM：该进程占用物理内存的百分比，占用越高，进程越耗费资源
# VSZ：该进程占用虚拟内存的大小，单位KB；
# RSS：该进程占用实际物理内存的大小，单位KB；
# TTY：该进程是在哪个终端中运行的。系统进程显示问号，其中tty1 - tty7代表本地控制台终端，tty1 - tty6是本地的字符界面终端，tty7是图形终端。pts/0-255代表虚拟终端（意味着最多支持256个远程终端）。
# STAT：进程状态。常见的状态有：R-运行，S-睡眠，T-停止，s-包含子进程，+-位于后台
# START：该进程的启动时间
# TIME：该进程占用CPU的运算时间，注意不是系统时间
# COMMAND：产生此进程的命令名

[root@core-pods-3 ~]# ps -le      # 查看系统中所有进程，使用linux标准命令格式
```

### 查看系统健康状态

这是一条比较重要的命令，命令是`top [选项]`
- -d 秒数：指定top命令每隔几秒更新。默认是3秒。

在top命令的交互模式当中可以执行的命令：
- ?或h：显示交互模式的帮助
- P：以cpu使用率排序，默认就是此项
- M：以内存的使用率排序
- N：以PID排序
- q：退出top

```bash
[root@core-pods-3 ~]# top
top - 06:59:32 up 45 days,  1:37,  1 user,  load average: 0.00, 0.00, 0.00
Tasks: 114 total,   2 running, 112 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.2 us,  0.0 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1031912 total,   165972 free,   215916 used,   650024 buff/cache
KiB Swap:   533500 total,   526400 free,     7100 used.   606316 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                        
    1 root      20   0  191040   5196   3724 S   0.0  0.5   1:16.77 systemd                                                                        
    2 root      20   0       0      0      0 S   0.0  0.0   0:00.29 kthreadd                                                                       
    4 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H                                                                   
    6 root      20   0       0      0      0 S   0.0  0.0   0:00.19 ksoftirqd/0                                                                    
    7 root      20   0       0      0      0 S   0.0  0.0   1:07.67 rcu_sched                                                                      
    8 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcu_bh                                                                         
    9 root      20   0       0      0      0 S   0.0  0.0   0:14.85 rcuos/0          
```

说明：

第一行信息为任务队列信息

|内容|说明|
|---|---|
|06:59:32|系统当前时间|
|06:59:32 up 45 days,  1:37|系统的运行时间，本机已经运行45天1小时37分钟，不一定准，比如断电|
|1 user|当前登录了1个用户|
|load average: 0.00, 0.00, 0.00|系统在之前1分钟、5分钟、15分钟的平均负载。对于单核，一般认为小于1时，负载较小。如果大于1，系统已经超出负荷；如果你是4核或8核，你可以认为超过8才是超负载，这块比较笼统|

第二行为进程信息

|内容|说明|
|---|---|
|Tasks: 114 total|系统中的进程总数|
|2 running|正在运行的进程数|
|112 sleeping|睡眠的进程|
|0 stopped|正在停止的进程|
|0 zombie|僵尸进程。如果不是0，需要手工检查僵尸进程，意味着这个进程正在终止，但是还没有终止完成|

第三行为CPU信息

|内容|说明|
|---|---|
|%Cpu(s):  0.2 us|用户模式占用的cpu百分比，即cpu被用户占了多少|
|0.0 sy|系统模式占用的cpu百分比，即cpu被系统占了多少|
|0.0 ni|改变过优先级的用户进程占用的cpu百分比|
|99.8 id|***空闲cpu的cpu百分比***，这个比较重要，如果太少看是否有非法进程，如果没有，你可能要扩容|
|0.0 wa|等待输入/输出的进程的占用cpu百分比|
|0.0 hi|硬中断请求服务占用的cpu百分比|
|0.0 si|软中断请求服务占用的cpu百分比|
|0.0 st|st（steal time）虚拟时间百分比。就是当有虚拟机时，虚拟cpu等待实际cpu的时间百分比|

第四行为内存信息

|内容|说明|
|---|---|
|KiB Mem :  1031912 total|物理内存的总量，单位KB|
|165972 free|空闲的物理内存数量|
|215916 used|已经使用的物理内存数量，我这边使用的是云主机，总共分配了1个G的内存，有215M内存在使用|
|650024 buff/cache|作为缓冲的内存数量|

第五行为交换分区（swap）信息

|内容|说明|
|---|---|
|KiB Swap:   533500 total|交换分区（虚拟内存）的总大小|
|526400 free|空闲交换分区的大小|
|7100 used|已经使用的交互分区的大小|
|606316 avail Mem|作为缓存的交互分区的大小|

上面信息主要看：平均负载、cpu空闲、内存空闲量这3个指标，默认是按照cpu从大到小排序，如果我们想按内存使用的大小排序，只需要按shift+M即可，如果想回去（看cpu排序），再按shift+P即可，top命令是比较耗费资源的，开开看一下就行，没必要一直开着，当我们需要的时候瞧一瞧就好了。

### 查看进程树

命令是`pstree [选项]`，选项：
-p：显示进程的pid
-u：显示进程的所属用户

```bash
[root@core-pods-3 ~]# pstree
[root@core-pods-3 ~]# pstree -u mysql
[root@core-pods-3 ~]# pstree -p 25394
[root@core-pods-3 ~]# top -Hp 25394   # 看25394这个进程下所有线程相关资源占用信息，用于线程诊断
```

## 进程管理

### Kill命令

查看kill支持的信号

```bash
[root@core-pods-3 ~]# kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX 
```

常用信号列举：

|信号代号|信号名称|说明|
|---|---|---|
|**1**|SIGHUP|该信号让进程立即关闭，然后重新读取配置文件之后重启。比如`service apache restart`|
|2|SIGINT|程序终止信号，用于终止前台进程。相当于输出ctrl+c快捷键|
|8|SIGFPE|在发生致命的算数运算错误时发出，不仅包括浮点数运算错误，还包括溢出及除数为0等其他所有的算术的错误|
|**9**|SIGKILL|用来立即结束程序的运行，本信号不能被阻塞、处理和忽略。一般用于强制终止进程|
|14|SIGALRM|始终定时信号，计算的是实际的时间或时钟时间，alarm函数使用该信号|
|**15**|SIGTERM|正常结束进程的信号，kill命令的**默认信号**。有时如果进程已经发生问题，这个信号是无法正常终止进程的，我们才会尝试SIGKILL信号，也就是信号9|
|18|SIGCONT|该信号可以让暂停的进程恢复执行，本信号不能被阻断|
|19|SIGSTOP|该信号可以暂停前台进程，相当于输入ctrl+z快捷键。本信号不能被阻断。|

```bash
[root@core-pods-3 ~]# kill -1 22354   # 重启该进程
[root@core-pods-3 ~]# kill -9 22354   # 杀死该进程

[root@core-pods-3 ~]# pstree -p | grep httpd
           |-httpd(26013)-+-httpd(26014)
           |              |-httpd(26015)
           |              |-httpd(26016)
           |              |-httpd(26017)
           |              `-httpd(26018)
[root@core-pods-3 ~]# kill 26014
[root@core-pods-3 ~]# pstree -p | grep httpd
           |-httpd(26013)-+-httpd(2674)
           |              |-httpd(26015)
           |              |-httpd(26016)
           |              |-httpd(26017)
           |              `-httpd(26018)
[root@core-pods-3 ~]# kill -1 26013
[root@core-pods-3 ~]# pstree -p | grep httpd
           |-httpd(26013)-+-httpd(2706)
           |              |-httpd(2707)
           |              |-httpd(2708)
           |              |-httpd(2709)
           |              `-httpd(2710)
```

### killall命令

killall命令是按照进程名杀死进程，命令是`killall [选项][信号] 进程名`，选项有：
- -i：交互式，询问是否要杀死某个进程
- -I：忽略进程名的大小写

```bash
[root@core-pods-3 ~]# killall -9 httpd
```

### pkill命令

和killall其实一样，命令是`pkill [选项][信号] 进程名`，选项：
- -t 终端号：按照终端号踢出用户

```bash
[root@core-pods-3 ~]# pkill -9 httpd     

# 踢出用户
[root@core-pods-3 ~]# ps aux | grep pts
root      3020  0.0  0.3 155464  4020 pts/1    R+   07:56   0:00 ps aux
root      3021  0.0  0.2 112824  2360 pts/1    S+   07:56   0:00 grep --color=auto pts
root     19051  0.0  0.9 159756  9904 ?        Ss   02:28   0:00 sshd: root@pts/1
root     27839  0.0  0.3 115692  3564 pts/1    Ss   05:19   0:00 -bash
[root@core-pods-3 ~]# w
 07:56:10 up 45 days,  2:33,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/1    64.104.44.97     05:19    1.00s  0.11s  0.11s -bash
[root@core-pods-3 ~]# pkill -9 -t pts/1
```

# 工作管理

## 把进程放入后台

有两种方法，第一种是直接放到后台执行的，在命令结尾加上`&`符号；第二种是后台暂停的，直接按ctrl+z放入后台队列。

```bash
[root@core-pods-3 ~]# tar -zcf etc.tar.gz /etc/ &   # 后台压缩etc下所有文件，注意“&”
[root@core-pods-3 ~]# top   # 在top执行过程中，按下ctrl+z快捷键（注意：ctrl+c是终止，他俩有区别）
[root@core-pods-3 ~]# jobs -l       # 显示后台工作。-l显示工作的pid， 也可以直接jobs，+号代表最近一个放入后台的工作，也是工作恢复时，默认恢复的工作，-号代表倒数第二个放入后台的工作
[1]   Stopped                 top
[2]-  Stopped                 top
[3]+  Stopped                 top
[root@core-pods-3 ~]# fg %3   # 将后台暂停的工作恢复到前台执行，“%工作号”， %可以省略，注意工作号不是pid
[root@core-pods-3 ~]# fg 1
[root@core-pods-3 ~]# jobs
[1]+  Stopped                 top
[root@core-pods-3 ~]# tar -zcf root.tar.gz /   # 此处点击ctrl+z放入后台
[root@core-pods-3 ~]# jobs
[1]-  Stopped                 top
[2]+  Stopped                 tar -zcf root.tar.gz /
[root@core-pods-3 ~]# bg 2     # 恢复执行第2条指令
[root@core-pods-3 ~]# jobs
[1]+  Stopped                 top
[2]-  Running                 tar -zcf root.tar.gz / &


# 注意后台恢复执行的命令是不能和前台有交互的，否则不能恢复到后台执行，如vim
[root@core-pods-3 ~]# vim aa
[3]+  Stopped                 vim aa
[root@core-pods-3 ~]# jobs
[1]-  Stopped                 top
[2]   Running                 tar -zcf root.tar.gz / &
[3]+  Stopped                 vim aa
[root@core-pods-3 ~]# 
[root@core-pods-3 ~]# ls
abc  bak  cde  cde~  cdz~  etc.tar.gz  ls.log  test  test2
[root@core-pods-3 ~]# bg 3
[3]+ vim aa &
```

# 系统资源查看

比如查看linux内核版本，操作系统信息等等

1. vmstat命令监控系统资源：vmstat [刷新延时 刷新次数]
   
   ```bash
   [root@core-pods-3 ~]# vmstat
   procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
    r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
    3  0   7100  65412  69184 677080    0    0     0     1    4    2  0  0 100  0  0
   [root@core-pods-3 ~]# vmstat 1 3
   procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
    r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
    3  0   7100  64520  69196 677796    0    0     0     1    4    2  0  0 100  0  0
    2  0   7100  64552  69196 677780    0    0     0     0 3793 5471 46  6 47  0  0
    1  0   7100  64084  69196 677960    0    0     0     0 3992 5780 48  7 45  0  0
   ```

2. dmesg开机时内核检测信息

   ```bash
   [root@core-pods-3 ~]# dmesg
   [root@core-pods-3 ~]# dmesg | grep CPU
   [    0.000000] x86/PAT: PAT not supported by CPU.
   [    0.000000] smpboot: Allowing 2 CPUs, 0 hotplug CPUs
   [    0.000000] setup_percpu: NR_CPUS:8192 nr_cpumask_bits:2 nr_cpu_ids:2 nr_node_ids:1
   [    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=2, Nodes=1
   [    0.000000]  RCU restricting CPUs from NR_CPUS=8192 to nr_cpu_ids=2.
   [    0.000000]  Offload RCU callbacks from all CPUs
   [    0.000000]  Offload RCU callbacks from CPUs: 0-1.
   [    0.065960] CPU: Physical Processor ID: 0
   [    0.065969] CPU: Processor Core ID: 0
   [    0.066043] mce: CPU supports 10 MCE banks
   [    0.209729] smpboot: CPU0: Intel QEMU Virtual CPU version (cpu64-rhel6) (family: 0x6, model: 0xd, stepping: 0x3)
   [    0.222392] smp: Bringing up secondary CPUs ...
   [    0.222979] .... node  #0, CPUs:      #1
   [    0.225712] smp: Brought up 1 node, 2 CPUs
   [    1.256306] ledtrig-cpu: registered to indicate activity on CPUs
   ```

3. free命令查看内存使用状态

  > free [-b|-k|-m|-g]  分别以字节/KB/MB/GB为单位查看内存使用信息

   ```bash
   [root@core-pods-3 ~]# free
                 total        used        free      shared  buff/cache   available
   Mem:        1031912      220924       75444       25600      735544      601308
   Swap:        533500        7100      526400
   [root@core-pods-3 ~]# free -g
                 total        used        free      shared  buff/cache   available
   Mem:              0           0           0           0           0           0
   Swap:             0           0           0
   [root@core-pods-3 ~]# free -m
                 total        used        free      shared  buff/cache   available
   Mem:           1007         215          72          25         719         587
   Swap:           520           6         514
   [root@core-pods-3 ~]# 
   ```

   > 缓冲和缓存的区别：简单来说缓存（cache）是用来加速数据从硬盘中“读取”的，而缓冲（buffer）是用来加速数据“写入”硬盘的。

4. 查看cpu信息：/proc/cpuinfo

   ```bash
    [root@core-pods-3 ~]# cat /proc/cpuinfo 
    processor       : 0
    vendor_id       : GenuineIntel
    cpu family      : 6
    model           : 13
    model name      : QEMU Virtual CPU version (cpu64-rhel6)
    stepping        : 3
    microcode       : 0x1
    cpu MHz         : 2599.998
    cache size      : 16384 KB
    physical id     : 0
    siblings        : 2
    core id         : 0
    cpu cores       : 2
    apicid          : 0
    initial apicid  : 0
    fpu             : yes
    fpu_exception   : yes
    cpuid level     : 13
    wp              : yes
    flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pse36 clflush mmx fxsr sse sse2 ss ht syscall nx pdpe1gb rdtscp lm rep_good nopl xtopology pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic popcnt aes xsave avx f16c rdrand hypervisor lahf_lm fsgsbase smep xsaveopt
    bugs            :
    bogomips        : 5199.99
    clflush size    : 64
    cache_alignment : 64
    address sizes   : 46 bits physical, 48 bits virtual
    power management:

    processor       : 1
    vendor_id       : GenuineIntel
    cpu family      : 6
    model           : 13
    model name      : QEMU Virtual CPU version (cpu64-rhel6)
    stepping        : 3
    microcode       : 0x1
    cpu MHz         : 2599.998
    cache size      : 16384 KB
    physical id     : 0
    siblings        : 2
    core id         : 1
    cpu cores       : 2
    apicid          : 1
    initial apicid  : 1
    fpu             : yes
    fpu_exception   : yes
    cpuid level     : 13
    wp              : yes
    flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pse36 clflush mmx fxsr sse sse2 ss ht syscall nx pdpe1gb rdtscp lm rep_good nopl xtopology pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic popcnt aes xsave avx f16c rdrand hypervisor lahf_lm fsgsbase smep xsaveopt
    bugs            :
    bogomips        : 5199.99
    clflush size    : 64
    cache_alignment : 64
    address sizes   : 46 bits physical, 48 bits virtual
    power management:
   ```

5. uptime命令

   显示启动时间和平均负载。其实就是top命令的第一行。

   ```bash
   [root@core-pods-3 ~]# uptime
    08:40:55 up 45 days,  3:18,  1 user,  load average: 1.13, 1.09, 0.96
   ```

6. 查看系统与内核相关信息

   ```bash
   [root@core-pods-3 ~]# uname 
   Linux
   [root@core-pods-3 ~]# uname -a      # 查看系统所有相关信息
   Linux core-pods-3.localdomain 4.10.4-1.el7.elrepo.x86_64 #1 SMP Sat Mar 18  12:50:10 EDT 2017 x86_64 x86_64 x86_64 GNU/Linux
   [root@core-pods-3 ~]# uname -r      # 查看内核版本
   4.10.4-1.el7.elrepo.x86_64      
   [root@core-pods-3 ~]# uname -s      # 查看内核名称，default
   Linux
   ```

7. 判断当前系统的位数

   ```bash
   [root@core-pods-3 ~]# file /bin/ls   # 查看文件类型连带可以看到位数
   /bin/ls: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=c8ada1f7095f6b2bb7ddc848e088c2d615c3743e, stripped
   ```

8. 查询当前linux系统的发行版本

   ```bash
   [root@core-pods-3 ~]# cat /etc/redhat-release   # centos 7
   CentOS Linux release 7.9.2009 (Core)

   [root@core-pods-3 ~]# lsb_release -a    # centos 6
   ```

9. 列出进程打开或使用的文件信息

   `lsof [选项]`
   - -c 字符串：只列出以字符串开头的进程打开的文件
   - -u 用户名：只列出某个用户的进程打开的文件
   - -p pid：列出某个pid进程打开的文件

   ```bash
   [root@core-pods-3 ~]# lsof -p 2142
   [root@core-pods-3 ~]# lsof -c init
   ```

# 系统定时任务

和windows的计划任务一个意思。linux有些备份任务可以设置成定时任务。

1. crond服务管理与访问控制
 
   启动crond后台服务，cron是定时的意思，d是daemon后台的意思。

   ```bash
   [root@core-pods-3 ~]# service crond restart
   [root@core-pods-3 ~]# chkconfig crond on
   [root@core-pods-3 ~]# systemctl enable crond
   [root@core-pods-3 ~]# ps aux | grep crond
   ```

2. 用户的crontab设置

   命令是`crontab [选项]`，选项有：
   - -e：编辑crontab定时任务
   - -l：查询crontab任务
   - -r：删除当前用户所有的crontab任务

   `crontab -e`进入编辑界面，会打开vim编辑，输入格式为：* * * * * 执行的任务

   |项目|含义|范围|
   |---|---|---|
   |第一个*号|一小时当中的第几分钟|0-59|
   |第二个*号|一天当中的第几小时|0-23|
   |第三个*号|一个月当中的第几天|1-31|
   |第四个*号|一年当中的第几月|1-12|
   |第五个*号|一周当中的星期几|0-7（0和7都代表星期日）|

   举几个例子

   |时间|含义|
   |---|---|
   |`45 22 * * * 命令`|在22点45分执行命令|
   |`0 17 * * 1 命令`|在每周1的17点0分执行命令|
   |`0 5 1,15 * * 命令`|每月1号和15号的凌晨5点0分执行命令|
   |`40 4 * * 1-5 命令`|每周一到周五的凌晨4点40分执行命令|
   |`*/10 4 * * * 命令`|每天的凌晨4点，每个10分钟执行一次命令|
   |`0 0 1,15 * 1 命令`|每月1号和15号，每周一的0点0分都会执行命令。注意：星期几和几号最好不要同时出现，因为他们定义的都是天，非常容易让管理员混乱。|

   特殊符号含义

   |特殊符号|含义|
   |---|---|
   |`*`|代表任何时间。比如第一个`“*”`就代表一小时中每分钟都执行一次的意思|
   |,|代表不连续的时间。比如`“0 8,12,16 * * * 命令”`，就代表在每天的8点0分，12点0分，16点0分都执行一次命令|
   |-|代表连续的时间范围。比如`“0 5 * * 1-6 命令”`，代表在周一到周六的凌晨5点0分执行命令。
   |`*/n`|代表每个多久执行一次。比如`“*/10 * * * * 命令”`，代表每隔10分钟就执行一遍名利那个

   举几个实际的job例子

   ```bash
   */5 * * * * /bin/echo "11" >> /tmp/test   # 每5分钟将11以追加方式写入test文件
   5 5 * * 2 /sbin/shutdown -r now    # 每周二重启一次
   0 5 1,10,15 * * /root/sh/autobak.sh  # 每月1，10，15号执行这个脚本进行备份

   [root@core-pods-3 ~]# crontab -l
   23 0 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" > /dev/null
   * * * * * /bin/echo "11" >> /tmp/test
   [root@core-pods-3 ~]# tail -f /tmp/test 
   11
   11
   ```

   注意定时任务里准备用日期作为文件名时，要加`\`转义，如`date=$(date +\%y\%m\%d)`，当然这里可能和系统版本的不同有关，这里是centos6。
