---
title: (3) Controlling nginx
date: 2020-12-28 16:37:47
categories:
- [学习, Nginx学习, Nginx文档学习]
tags:
- nginx
---

```
参考：http://nginx.org/en/docs/control.html
 
 
1.查看master进程pid：看nginx.conf配置中 pid 配置所指向的path;
 
 
 
2.nginx signals （nginx信号集）
nginx自身可以通过各种信号来操控，我们有两种方式来向 master 进程发送信号:
（1）通过 nginx -s signal 来操作：产生一个新进程，该进程通过 nginx.pid 文件
     得到 master 进程的 pid，然后把对应的信号发送到 master，之后退出，这种
     进程被称为 signaller。
（2）通过 kill 命令手动发送：kill操作的是nginx -s signal映射到的真实信号
关键性的信号操作完成后，等待一段时间，避免时间窗口的影响
 
master进程可以通过下面的信号来操控：
TERM, INT：快速关闭、强制退出，慎用 （映射nginx -s stop）
QUIT：正常关闭，优雅地关闭 （映射nginx -s quit）
HUP：重新进行配置文件解析、共享内存申请，
     协同时区变化 (仅对 FreeBSD and Linux), 
     伴随新的配置开启一个新的worker进程, 
     正常关闭老的进程，实现无缝重启 （映射nginx -s reload）
USR1：重新打开日志文件 （映射nginx -s reopen）
USR2：更新可执行文件，热更新，热更新无法通过提供的command实现，只能手动发送信号，
      热更新之后，如果确定业务没问题，尽可能让旧的 master 进程退出
WINCH：正常关闭worker进程
 
worker进程也可以通过下面信号来操作：
（事实上，操作worker进程是没有太大必要的，而且尽量不要直接向 worker 进程发送信号）
TERM, INT：快速关闭、强制退出
QUIT：正常关闭，优雅地关闭
USR1：重新打开日志文件
WINCH：异常调试时终止进程 (需要启用debug_points配置)
       （参考：http://nginx.org/en/docs/ngx_core_module.html#debug_points）
 
尝试：
[root@jalen run]# kill -QUIT $(cat /var/run/nginx.pid)  --退出
[root@jalen run]# kill -TERM $(cat /var/run/nginx.pid)  --强制退出
[root@jalen run]# kill -WINCH $(cat /var/run/nginx.pid) --关闭worker进程
 
 
 
3.reopen可用于日志切割
官方叫Rotating Log-files，先rename日志文件，再发送USR1信号给master进程做响应处理
测试：
[root@jalen nginx]# mv access.log access.log.0
[root@jalen nginx]# kill -USR1 $(cat /var/run/nginx.pid)
[root@jalen nginx]# sleep 1
[root@jalen nginx]# gzip access.log.0   # 压缩切割后的log
sleep 1是必须的，因为从master向worker发送USR1信号，到worker真正打开新的access.log
文件期间这段时间还是向access.log.0写入，这是为了保证log完整性，防止直接压缩导致log丢失
 
 
 
4.master与worker进程之间通信
master与worker进程之间通信不是使用 kill 函数，而是通过管道，即nginx channel，
一端（master）写入信息（比如信号信息），另外一端（worker）收取信息，
worker刚启动时，就会加入epoll/kqueue事件调度器，对后续数据流作出感知。
当然，我们也可以绕过 master 进程，直接向 worker 进程发送信号。
对nginx channel的描述网上有很多对源码的分析案例。
 
 
 
5.修改配置（HUP）
发送HUP signal给master进程，master进程先作语法校验，然后尝试应用新配置（打开日志文件
和新的socket），失败回滚到老的配置，成功则启动新的worker进程，并通知旧的进程优雅地关
闭，旧的进程关闭socket监听并继续为旧的client提供服务，当为所有客户端提供服务后，旧的
工作进程将关闭。
测试（我这里执行后没有出现xx is shutting down，可能本地没有正在执行的请求）：
[root@jalen nginx]# ps axw -o pid,ppid,user,%cpu,vsz,wchan,command | egrep '(nginx|PID)'
  PID  PPID USER     %CPU    VSZ WCHAN  COMMAND
30989     1 root      0.0  46508 sigsus nginx: master process nginx
30990 30989 nginx     0.0  47040 ep_pol nginx: worker process
30991 30989 nginx     0.0  47040 ep_pol nginx: worker process
30994 30755 root      0.0 112828 pipe_w grep -E --color=auto (nginx|PID)
[root@jalen nginx]# kill -HUP 30989
[root@jalen nginx]# ps axw -o pid,ppid,user,%cpu,vsz,wchan,command | egrep '(nginx|PID)'
  PID  PPID USER     %CPU    VSZ WCHAN  COMMAND
30989     1 root      0.0  49500 sigsus nginx: master process nginx
30991 30989 nginx     0.0  47040 ep_pol nginx: worker process is shutting down (nginx)
31004 30989 nginx     0.0  49504 ep_pol nginx: worker process
31005 30989 nginx     0.0  49504 ep_pol nginx: worker process
31007 30755 root      0.0 112828 pipe_w grep -E --color=auto (nginx|PID)
[root@jalen nginx]# ps axw -o pid,ppid,user,%cpu,vsz,wchan,command | egrep '(nginx|PID)'
  PID  PPID USER     %CPU    VSZ WCHAN  COMMAND
30989     1 root      0.0  49500 sigsus nginx: master process nginx
31004 30989 nginx     0.0  49504 ep_pol nginx: worker process
31005 30989 nginx     0.0  49504 ep_pol nginx: worker process
31009 30755 root      0.0 112828 pipe_w grep -E --color=auto (nginx|PID)
 
 
 
6.快速升级可执行文件
首先将旧文件替换为新的可执行文件。然后发送USR2给master进程。
master进程首先将其含有进程ID的文件重命名为带有.oldbin后缀的新文件，如nginx.pid.oldbin。
然后启动这个新的可执行文件，产生一个新的worker进程。
当然，所有的worker进程(old and new ones)会继续接收和处理它们的requests。
如果此时发送WINCH信号给第一个master进程，它将会发送消息给它的worker进程并请求关闭。
过一段时间后（old requests完成），就只会有新的worker进程在工作了。
 
注意：旧的master进程不会关闭它的listen sockets，如果需要它可以再次启动它的worker进程，
如果新的可执行文件工作不太理想，可以做下面操作（二选一）：
（1）将HUP信号发送到旧的master进程，旧的master进程将启动新的worker进程而无需重新读取配置。
之后，可以通过将QUIT信号发送到新的master进程来正常关闭所有新进程。
（2）将TERM信号发送到新的主进程，然后它将向其worker进程发送一条消息，要求它们强制立即退出。
当新的master进程退出时，旧的master进程将自动启动新的worker进程。
 
如果新的master进程退出，则旧的master进程将含有进程ID的文件名中的.oldbin后缀丢弃。
如果升级成功，则应该将QUIT信号发送到旧的master进程，即只保留新的进程：
 
 
 
 
 
附录：
1.locate命令command not found
linux一切皆文件，
locate使用到的数据库文件是/var/lib/mlocate/mlocate.db，
 
安装：
[root@jalen usr]# yum install mlocate
 
安装后直接使用该命令会报db file不存在需要先update一下：
[root@jalen usr]# locate nginx
locate: can not stat () `/var/lib/mlocate/mlocate.db`: No such file or directory
[root@jalen usr]# updatedb
 
搜索限制设置
[root@jalen log]# cat /etc/updatedb.conf
PRUNE_BIND_MOUNTS = "yes"
PRUNEFS = "9p afs anon_inodefs auto ... ...fuse.glusterfs ceph fuse.ceph"
PRUNENAMES = ".git .hg .svn"
PRUNEPATHS = "/afs /media ... ... /var/lib/ceph"
搜索限制配置说明：
PRUNE_BIND_MOUNTS = "yes" 开启搜索限制，如果为’no’则表示不开启搜索限制；
PRUNEFS = 搜索时，不搜索的文件系统；
PRUNENAMES = 搜索时，不搜索的文件类型；
PRUNEPATHS = 搜索时，不搜索的路径； 
 
 
 
2.linux下which、whereis、locate、find区别
which：常用于查找可直接执行的命令。只能查找可执行文件，该命令基本只在$PATH路径中搜索，
       查找范围最小，查找速度快。默认只返回第一个匹配的文件路径，通过选项 -a 可以返回
       所有匹配结果。
       [root@jalen ~]# which nginx
whereis：不只可以查找命令，其他文件类型都可以（man中说只能查命令、源文件和man文件，实
       际测试可以查大多数文件）。在$PATH路径基础上增加了一些系统目录的查找，查找范围
       比which稍大，查找速度快。可以通过 -b 选项，限定只搜索二进制文件。
       [root@jalen home]# whereis nginx
locate：超快速查找任意文件。它会从linux内置的索引数据库查找文件的路径，索引速度超快。
       刚刚新建的文件可能需要一定时间才能加入该索引数据库，可以通过执行updatedb命令来
       强制更新一次索引，这样确保不会遗漏文件。该命令通常会返回大量匹配项，可以使用-r
       选项通过正则表达式来精确匹配。
       [root@jalen usr]# locate nginx
find：直接搜索整个文件目录，默认直接从根目录开始搜索，建议在以上命令都无法解决问题时才
       用它，功能最强大但速度超慢。除非你指定一个很小的搜索范围。通过 -name 选项指定要
       查找的文件名，支持通配符。
       [root@jalen usr]# find ./ -name nginx
（参考：https://zhuanlan.zhihu.com/p/35727707）
 
 
 
3.linux的/var/run目录作用 -- 运行时变量数据存储
/var/run 目录中存放的是自系统启动以来描述系统信息的文件。
比较常见的用途是daemon进程将自己的pid保存到这个目录。
除了保存进程的pid之外也有其他的作用，比如utmp文件，就是用来记录机器的启动时间以及当前登陆用户的。
FHS标准要求这个文件夹中的文件必须是在系统启动的时候清空（有选择地），以便建立新的文件。
（参考：https://www.pathname.com/fhs/2.2/fhs-5.13.html）
 
 
 
4.查看centos cpu相关信息：
[root@jalen nginx]# cat /proc/cpuinfo
[root@jalen nginx]# lscpu
```
