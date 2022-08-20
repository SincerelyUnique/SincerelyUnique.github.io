---
title: ubuntu简单安装redis
date: 2020-06-12 23:57:20
tags:
- redis
- linux
categories:
- [学习, DevOPS, Ubuntu]
---

```
1. 安装redis
（1）安装redis server： sudo apt-get install redis-server  （安装后redis默认启动）
（2）查看redis进程： ps -aux|grep redis  （127.0.0.1:6379对应进程号）
（3）通过pid查看redis服务状态： netstat -nlt|grep 6379    （tcp/tcp 6均处于listen状态）
（4）查看redis服务器状态：sudo /etc/init.d/redis-server status   （处于active (running)状态）
（5）查看redis安装路径（redis.conf所在目录）： whereis redis  (一般默认在： /etc/redis)
（6）查看redis-cli安装路径（redis客户端）： whereis redis-cli   (一般默认在： /usr/bin/redis-cli)
（7）查看redis-server安装路径（redis服务器）： whereis redis-server  (一般默认在： /usr/bin/redis-server)
（8）redis-server log： /var/log/redis/redis-server.log    (/etc/redis/redis.conf 的默认配置的路径，必须设置，影响redis数据磁盘写入)
（9）RDB路径： 默认为 /etc/redis     (/etc/redis/redis.conf 的默认配置的路径，启用持久化时必须设置，且必须是完整路径)
 或去官网下载tar.gz压缩文件，解压后make test   & make install
 
 
2. 配置redis
（1）配置文件路径：/etc/redis/redis.conf
（2）设置服务器密码（默认是不需要密码的）： requirepass <password>  （设过密码后，每次redis-cli登录后都需要输入auth <password>）
（3）修改端口号： port <port>
 
 
3. 启动、关闭redis
（1）关闭redis-server： sudo /etc/init.d/redis-server stop   
     (第一次通过apt安装默认启动后尝试使用redis-cli shutdown无效，再尝试使用kill命令，但是每次杀过会换一个新的pid继续运行，猜测是因为新装的redis默认开启了redis daemon进程, 但不敢肯定)
（2）启动： sudo redis-server     （ctrl + z 挂起该进程，此时该进程还存在于linux系统进程中且处于被中断状态，可恢复，此时使用redis-cli连接时无响应）
（3）通过指定配置文件启动： sudo redis-server /path/to/redis.conf
（4）未配置登录密码直接关闭： sudo redis-cli shutdown
（5）配置密码时关闭： sudo redis-cli -a <password> shutdown
（6）杀死进程： sudo kill -9 <pid>
（7）测试redis-server： 进入redis-cli，输入PING，返回PONG，此时正常
（8）使用redis-cli远程关闭redis-server： sudo redis-cli -h 127.0.0.1 -p 6379 -a <password> shutdown
 
 
 
问题一：
当在/etc/redis/redis.conf中设置requirepass后再使用redis-cli登录后使用，直接set name jalen会报错（(error) NOAUTH Authentication required.），此时需要先输入auth <password>登录，然后再进行操作
 
问题二：
如果不是使用root权限启动redis，使用ctrl + c去关闭redis-server进程，或者使用redis-cli远程关闭redis-server进程，都是没有办法成功的，因为此时redis-server没有权限访问/etc/redis目录导致在进程退出时写磁盘快照RDB失败，进程不予退出，即can't exit。
此时直接ps查询进程号，使用kill -9强制关闭，否则无法关闭，或者使用ctrl + z 先挂起该进程，然后ps拿到该进程号后kill掉
Error Log：
24033:M 12 Jun 23:23:12.953 # User requested shutdown...
24033:M 12 Jun 23:23:12.953 * Saving the final RDB snapshot before exiting.
24033:M 12 Jun 23:23:12.953 # Failed opening the RDB file dump.rdb (in server root dir /etc/redis) for saving: Permission denied
24033:M 12 Jun 23:23:12.953 # Error trying to save the DB, can't exit.
24033:M 12 Jun 23:23:12.953 # SIGTERM received but errors trying to shut down the server, check the logs for more information
 
问题三：
当我们关机后重启会发现redis会默认启动，这是因为我们将redis的daemon设为yes，并且在/etc/init.d目录（linux开机会执行该目录下文件）下可以看到redis-server启动脚本
```
