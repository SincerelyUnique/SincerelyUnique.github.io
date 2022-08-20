---
title: 简单使用redis分布式锁
date: 2021-08-09 16:44:59
tags:
- redis
- concurrent
categories:
- [学习, 数据库学习, Redis]
---

```python
python使用redis分布式锁
 
1.使用分布式锁需要注意的细节：
 （1）互斥性。任何时刻只有一个客户端能持有锁
 （2）避免死锁。即使一个客户端持有锁的期间崩溃而没有主动释放锁，也需要保证后续其他客户端能够加锁成功
 （3）加锁和解锁必须是同一个客户端，客户端自己不能把其他客户端加的锁给释放了。
 
2.使用redis实现分布式锁，主要是使用了3个命令 setnx + expire + del， 但是注意这些命令不具备原子性，
  比如一个线程在setnx后抛出异常导致没有执行expire，此时会导致锁不释放并一直阻塞，所以一定要保证这
  些命令搭配使用的原子性
 
3.了解redis的setnx命令，setnx是SET if Not eXists的简称。
  setnx命令的返回特性：命令在设置成功时返回 1 ， 设置失败时返回 0 
  中文文档地址：http://redisdoc.com/string/setnx.html
  英文文档地址：https://redis.io/commands/setnx
 
  了解redis的expire命令，设置锁过期时间
  中文文档地址：http://redisdoc.com/expire/expire.html
  英文文档地址：https://redis.io/commands/expire
 
  了解redis的del命令，删除并释放锁
  中文文档地址：http://redisdoc.com/database/del.html
  英文文档地址：https://redis.io/commands/del
 
4.代码
def acquire_lock(conn, lock_name, acquire_time=125, time_out=120):
    """
    Acquire a distribute lock according to redis
    :param conn: type redis.client.Redis, redis connection
    :param lock_name: type str, lock name
    :param acquire_time: type int, The time the client waits to acquire the lock
    :param time_out: type int, lock timeout time
    :return: return identifier if get lock, else return false
    """
    identifier = str(uuid.uuid4())
    end = time.time() + acquire_time
    lock = "string:lock:" + lock_name
    while time.time() < end:
        if conn.set(lock, identifier, ex=time_out, nx=True):
            return identifier
        time.sleep(1)
    return False
 
 
def release_lock(conn, lock_name, identifier):
    """
    Release a redis distributed lock to allow other thread acquire the lock
    :param conn: type redis.client.Redis, redis connection
    :param lock_name: type str, lock name
    :param identifier: type str, lock value - identifier(uuid)
    :return: return True if lock release success, else False
    """
    unlock_script = """
        if redis.call("get",KEYS[1]) == ARGV[1] then
            return redis.call("del",KEYS[1])
        else
            return 0
        end
        """
    lock = "string:lock:" + lock_name
    unlock = conn.register_script(unlock_script)
    return unlock(keys=[lock], args=[identifier])
 
 
5.建议参考官网（https://redis.io/topics/distlock）这里有一些基于不同语言（如python、java等）实现的第三方的相对稳定的redlock
 
 
参考：
  https://zhuanlan.zhihu.com/p/112016634
  https://www.cnblogs.com/zknublx/p/11722074.html
 
问题：
  安全性，如果redis节点挂了或slave提升为master期间会出现问题
  性能，acquire time自旋，如果持久获取不到锁会导致线程阻塞，如果只是需要乐观锁可以这样试试。
```
