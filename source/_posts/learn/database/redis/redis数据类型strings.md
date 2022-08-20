---
title: redis数据类型strings
date: 2020-07-14 14:03:28
tags:
- redis
categories:
- [学习, 数据库学习, Redis]
---

```
Redis数据类型介绍(https://redis.io/topics/data-types-intro#strings)
1. strings：Binary-safe strings，结构简单
2. lists：有序-根据插入顺序排列，元素类型-string，数据结构-基于链表实现（linked lists）
3. sets：元素类型-string，元素唯一且无序
4. sorted sets：每个元素关联一个浮点数值叫score，每个元素总是按照score排序，可以做范围查询，如取前10个元素或取后10个元素
5. hashes：类似字典将属性和值关联起来，属性和值的类型都是string，比较类似python或ruby的hash()函数，适用于存储对象
6. bit arrays：或称simply bitmaps，比特数组，位数组，用于处理二进制位数组
7. hyperloglogs：概率数据结构，用于估计集合的基数，用来做基数统计的算法
8. streams：类似于map entry的append-only集合，提供了一个抽象的日志数据类型


1. redis keys（redis的key）
二进制安全（binary safe），可以为空，规则：
（1）key不要太长：一是耗内存，二是查找时需要耗费时间做key值比较（key-comparisons），三是网络通信时占用带宽，
         如果实际需要大的key，可以做一次hash运算，如使用SHA1.
（2）key不要太短：保证key值的（业务）可读性，比如使用"user:1000:followers"而不是"u1000flw"
（3）尝试使用特定的模式去定义key：如"object-type:id"，"user:1000"，如果遇到多个单词，可以使用点号（.）或破折号（-）分隔，
         如"comment:1234:reply.to"或"comment:1234:reply-to"
（4）key值最大为512M


2. redis strings（redis的string）
（1）通过一个string的key关联一个string类型的value，strings也是Memcached唯一指定的数据类型
（2）可以通过string来缓存一个html标签或者页面
（3）查询所有key：keys *，生产环境不允许使用该命令
（4）登陆验证： auth ******，设置了requirepass后使用
（5）清空缓存：flushall，谨慎使用
（6）value最大不能超过512M
（7）可以将一张图片存储到redis内，但是要注意存储前需要进行序列化，如python的pickle，jdk或spring自带的序列化工具等
（8）添加：set mykey somevalue， 此set操作会替换之前已经存在的值
         添加：set mykey newval nx，只新增，即此set操作作用于一个已存在的key上会返回nil，不会替换原值，作用于一个不存在的key则新增
         添加：set mykey newval xx，只修改，即此set操作作用于一个已存在的key上会替换原值，作用于一个不存在的key上无效并返回nil
（9）查询：get mykey
（10）原子递增：会将string类型的数字解析为integer，然后执行相关计算操作，如incr，incrby，decr，decrby，
        该操作在多客户端模式下操作是原子的，不存在数据访问冲突
        设计数器：set counter 100， 默认100
        递增操作：incr counter，执行后为101
                          incr counter，执行后为102
                          get counter， 返回102
（11）使用getset设置新value，返回旧value，应用场景：比如记录每小时的网站访问数（结合incr计数器，获取过去1小时访问量后清零，getset counter 0）
        设值：set mykey oldvalue
        设值：getset mykey newvalue， 返回oldvalue
        取值：get mykey，返回newvalue
（12）一次设置多个key-value对，或一次获取多个key对应的value，可以减少延迟（网络延迟等）
        设值：mset a 10 b 20 c 30
        取值：mget a b c， 返回一个数组，a对应数组下标第一个值，依此类推


Altering and querying the key space（修改和查询key）
有些命令并不针对某些特殊的数据类型，但是对于key的操作非常有用，可以作用于任何类型的key值上面
（1）exists：返回integer类型的0或1，用来表明key是否存在； del：删除key和其相关的value，返回integer类型的0（没有该key）或1（删除成功）
         命令：exists mykey， 返回1，说明该key已存在
                   del mykey，返回1，删除mykey和其对应的value成功
                   exists mykey，返回0，说明key不存在
                   del mykey， 返回0，该key不存在
（2）type：返回value的类型
         命令：type mykey， 如果mykey不存在则返回none，
                    set mykey 123，
                    type mykey，返回string


Redis expires：keys with limited time to live（redis过期设置：限制key的存活时间）
可以为1个key设置超时，当时间到了自动销毁key，类似于用户操作del命令
（1）可以设置秒s或者毫秒ms
（2）过期时间总是精确到毫秒
（3）过期信息会被复制并保留到磁盘上，即便redis server停掉
（4）使用：
         命令：set mykey somevalue
                   expire mykey 5， 设置mykey 5s过期
                   get mykey， 未到5s，返回somevalue
                   get mykey， 未到5s，返回somevalue
                   ttl mykery， 返回秒，即还剩多少秒过期
                   get mykey， 过了5s后，返回nil
                   ttl mykery， 返回integer类型-2，说明已过期
（5）使用persist移除expire设置，仅在key处于ttl时间范围内可以生效，key一旦过期该命令也就无法生效了
         命令：set mykey somevalue
                   expire mykey 20， 设置20s过期
                   get mykey， 未到20s，返回somevalue
                   ttl mykey， 未到20s，查看还有多少秒过期
                   persisit mykey， 未到20s，可以解除20s的过期时间限制，返回interger类型1说明解除成功，返回0说明mykey已经是永久的了
                   ttl mykey， 返回integer类型-1，说明mykey已被改为永久，不存在过期时间
（6）直接使用set命令赋值，同时设置过期时间
         命令：set mykey 100 ex 10， 赋值为100，设置10s过期
                    ttl mykey， 返回还剩多少秒过期
（7）设置为毫秒的命令：pexpire，pttl
```
