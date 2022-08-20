---
title: redis哨兵简单配置
date: 2020-09-04 18:41:23
tags:
- redis
categories:
- [学习, 数据库学习, Redis]
---

```
简单部署redis哨兵
 
说明：
redis版本：6.0.4
1台主服务器（master）、1台从服务器（slave）、3台哨兵（sentinel）服务器。
 
公司内部有自己固定的yum源，我可以通过yum直接安装redis，
如果没有可以去官网下载最新版redis tar.gz包，解压后进入redis的src目录执
行make & make install。
由于一般centos7默认gcc版本是4.8.5，使用make编译最新版redis时须升级gcc版本.
 
 
1. 配置
（1）master配置（ip: 192.168.0.1）
     bind 0.0.0.0
     port 6379
     daemonize yes
     masterauth "123456"
     requirepass 123456
     replica-read-only yes
 
（2）slave配置（ip: 192.168.0.2）
     bind 0.0.0.0
     port 6379
     daemonize yes
     masterauth "123456"
     requirepass 123456
     replicaof 192.168.0.1 6379
     replica-read-only yes
 
（3）sentinel配置（ip: 192.168.0.3；192.168.0.4；192.168.0.5）
     port 26379
     daemonize yes
     sentinel monitor mymaster 192.168.0.1 6379 2
     sentinel auth-pass mymaster 123456
     sentinel down-after-milliseconds mymaster 30000
     sentinel parallel-syncs mymaster 1
     sentinel failover-timeout mymaster 180000
 
 
2. 启动（注意顺序）
启动master：/opt/redis/bin/redis-server /etc/redis/redis.conf
启动slave：/opt/redis/bin/redis-server /etc/redis/redis.conf
启动4个sentinel：/opt/redis/bin/redis-sentinel /etc/redis/redis-sentinel.conf 
 
 
3. 备注（最近玩redis，可能有些谬误，仅供参考）
（1）如果条件允许，最好配置多个slave，提高HA（可用性），我这里只配置1个slave；
（2）查看redis版本：/opt/redis/bin/redis-server --version
（3）查看redis主从信息：info replication  （在redis-cli模式下执行，返回role）
（4）主观下线（sdown）：根据down-after-milliseconds mymaster属性，我这里是30s后sdown，redis默认也是30s
（5）客观下线（odown）：哨兵各自发起主观下线后，其中一个哨兵发起投票，一旦quorum达到2个，执行odwon
（6）哨兵投票最好半数以上才通过，我这边设置的投票quorum（法定人数）是2，即3个哨兵中2个投票通过即可执行odwon
（7）部署哨兵尽可能不要在同一个网段，或者不要在一个location，更不要和redis server在一个vm box里，一挂全挂
（8）如果master下线：
     30s内恢复：继续担任master角色，如果仅以rdb做持久化，master的write有可能会丢失，这取决于配置，建议aof
     30s后恢复：由于此时哨兵已完全确认master停止心跳，所以同时发起sdown，继而发起odown，所以slave晋升为master，
                此时之前的master重启，则角色变更，变为slave
（9）master down掉的30s内，由于master/slave模式默认为读/写分离，故无法向master写，会丢数据，可以开启aof，
     同时建议程序捕捉异常信息（通常是timeout或connect refuse异常），并在程序里通过smtp或其他工具alert给管理员
（10）slave也可以开启写模式（replica-read-only设为no），但为防止滥用和安全考虑，不建议打开，
     并且slave即便打开replica-read-only写数据，也不会同步到master side，通常我们所说主从同步是指数据从master
     流向slave，而不是从slave流向master，不打开该配置也是为了考虑数据的一致性。
（12）有考虑用nginx的upstream做LB，想了下，走nginx其实我们只会给读做LB而不是写（因为只往master上写，不需要），
     而事实是我们app的QPS不高，按redis benchmark测试每秒可达10w的request来讲，完全没必要，
     要说必要的话就是在odown的30s内，redis有可能会无法读数据（如果我们只连master）
（13）app里使用redis最好创建connect pool，从pool里获取client 连接，利于管理
（14）app中我尝试的是使用redis sentinel工具连接3个sentinel，通过sentinel监控master的变更，同时也可以
     通过sentinel工具获取redis client，再使用client执行redis命令（python我用的是flask_redis_sentinel）
（15）哨兵数量最好是3个或3个以上，官网doc里也有一些少于3个的example，可以看一下
（16）网上redis + nginx直接玩的很少
（17）主从复制（master-slave replication）通常是master在写入数据时立即sync到slave side
（18）redis如果不是集群模式，默认是16个database，而通常我们使用的都是database 0
（19）注意如果设置了requirepass，在sentinel.conf中sentinel auth-pass要在sentinel monitor下面一行
 
 
 
4. 参考
https://redis.io/topics/sentinel
https://github.com/underyx/flask-redis
https://github.com/exponea/flask-redis-sentinel
https://redis-py.readthedocs.io/en/stable/_modules/redis/client.html

```
