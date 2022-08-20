---
title: 记一次ORA-00060 Deadlock detected的场景
date: 2020-07-09 17:48:04
tags:
- oracle
- sql
categories:
- [学习, 数据库学习, Oracle问题记录]
---

## 查看被锁的表

```sql
select object_name, machine, s.sid, s.serial#
  from v$locked_object l, dba_objects o, v$session s
 where l.object_id = o.OBJECT_ID
       and l.session_id = s.sid;
```

## 强制解锁

sid -> 277, serial# -> 1817,  --需要dba权限，如果不是dba的话在客户端强制断开查询也会解锁恢复

```
alter system kill session '277,1817';
```

## 死锁场景模拟（打开2个sql执行窗口）

```
（1）窗口1执行sql1，不commit；
（2）窗口2执行sql2，不commit；
（3）窗口1执行sql2，hang住， 一直loading等待窗口2commit
（4）窗口2执行sql1，此时因为出现循环依赖直接导致窗口1报错ORA-00060，窗口1进程退出，窗口2进程hang住，
    查db发现该表被锁，强制终止该查询后才恢复（网上说oracle默认一段时间后也是会自动恢复的，未尝试）
 
sql1： update table set column='123' where ID='101'; 
sql2： update table set column='321' where ID='102';
```

## 寻找原因

sql语句使用了merge into，并发操作造成，这个网上有很多描述，因为merge into同时执行insert或update操作，会锁住当前所有操作行，如果同一时间有多个线程对同一张表执行merge into，会导致deadlock，由于我们使用的是xxl-job搭建的定时任务框架，由一个调度中心和多个执行器组成，而该sql所在的job在执行时也是单线程，不存在并发行为，然后查询原因，发现该job被另外一台机器IP trigger了（xxl-job可以看到触发执行器执行的调度中心IP地址），由于我们本身就有自己的一台机器作为调度中心，现在突然变为两台了，当两台机器同一时刻触发同一任务时，就会出现2个线程共同执行一样的merge into语句，此时导致oracle数据库种该表出现并发死锁，抛出异常.

## 最终猜测

基本可以确定有人在本地启动了job调度中心，而他的调度中心配置得accessToken（调度中心和执行器之间的通信令牌）和我们产线执行器中配置是一样的，所以和产线执行器产生了通信（因为使用一个数据库，且在注册时没有加锁（for update），执行器会自动在内网中识别出调度中心机器ip，或者说调度中心会自动trigger定时任务到各台执行器上）

## 处理

其实应该对merge into操作做并发处理，比如加锁之类的，但是因为本身job是单线程执行，不存在并发场景，所以先不考虑，因此先尝试修改accessToken（修改得更复杂），并将一些项目配置信息提取到外部，不能hardcode，保证安全至于那个陌生的IP，就不去查了，不太好查
