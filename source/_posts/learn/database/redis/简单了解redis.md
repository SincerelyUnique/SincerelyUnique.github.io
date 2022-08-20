---
title: 简单了解redis
date: 2020-06-25 20:24:31
tags:
- redis
categories:
- [学习, 数据库学习, Redis]
---

```
redis是一个开源的（BSD协议-伯克利软件发行版）、内存数据结构仓库，可用作为数据库、缓存和消息代理。
redis支持像strings、hashes、lists、sets、sorted sets（范围查询）这些数据结构，也支持类似bitmaps、hyperloglogs、geospatial indexes（半径查询）和streams。
redis有内置的replication、lua脚本、LRU eviction（LRU淘汰策略）、事务和不同级别的磁盘持久操作，同时通过redis sentinel（哨兵）和redis集群自动分区提供强大的高可用性（HA）。

我们可以在这些redis数据类型上执行原子操作，如append字符串，增加一个hash的值，在list中放入一个元素，计算交集、并集和差集，或是获取sorted set中排名最高的成员。

为了获取优异的性能，redis使用in-memory dataset工作，根据实际情况，我们可以做持久操作比如dump the dataset到磁盘（RDB），或者是appending each command to a log（AOF）。
如果我们只是需要使用一个功能丰富的、可以连网的缓存，持久化操作也是可以选择禁用的。

redis也支持简单设置master-slave asynchronous replication（主从异步复制）， with very fast non-blocking first synchronization, auto-reconnection with partial resynchronization on net split.（在网络变动时进行一个快速非阻塞的数据同步操作--即在自动重连后部分重新同步，感觉翻译不通顺...）。

其他特点：
（1）Transactions
（2）Pub/Sub
（3）Lua scripting
（4）Keys with a limited time-to-live
（5）LRU eviction of keys
（6）Automatic failover

我们可以使用多种语言连接redis
redis是用ANSI C写的，可以在大部分POSIX系统（ 可移植操作系统接口）工作如Linux，*BSD，OS X上面而不需要额外的任何依赖，Linux和OS X是两个可以部署redis的系统，而且久经测试，建议使用Linux去部署使用。
redis可以在Solaris-derived系统比如SmartOS上工作，针对windows系统，暂时没有官方的支持（windows上面好像是微软自行维护的一个redis分支吧）。

 

来自：https://redis.io/topics/introduction
```
