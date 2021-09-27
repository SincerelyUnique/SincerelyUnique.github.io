---
title: redis数据类型lists
date: 2020-07-14 16:37:56
tags:
- redis
categories:
---

```
redis lists（参考https://redis.io/topics/data-types-intro#lists）
从非常普遍的角度来看，列表只是一系列有序元素，比如[10,20,1,2,3]，通常list有两种实现方式，一种是以数组方式，
另外一种是以链表方式实现，而redis的lists实现就是以链表方式实现，有优点也有缺点。
优点：链表这种实现方式在插入时会非常快，无论当前list的长度是10还是100000，使用lpush命令插入的效率是一样的
缺点：查询时没有数组快，数组可以通过index直接定位到元素，而链表只能链式查找，数据量越大，效率越低

redis使用链表是因为对数据库系统而言，至关重要的是能够以非常快的方式将元素添加到很长的list中，
另外一个优点是：Redis Lists can be taken at constant length in constant time.

如果我们想要快速访问一个大的集合的中间元素，可以使用sorted sets。


First steps with Redis Lists（使用redis lists）
（1）使用lpush从list左侧（头部）添加元素，
         使用rpush从list右侧（尾部）添加元素，
         使用lrange从list的左侧开始通过下标提取元素
         命令：lpush mylist A
                    rpush mylist B
                    lpush mylist C
                    lrange mylist 0 -1， 0是指list左侧第一个元素，-1是指list最后一个元素（-2则指list倒数第二个元素）
（2）一次放入多个元素
         命令：rpush mylist 1 2 3 4 5 "foo bar"
                    lrange mylist 0 -1
（3）弹出元素
         使用rpop从list右侧弹出元素：rpop mylist
         使用lpop从list左侧弹出元素：lpop mylist
         从一个空list弹出元素返回nil


Common use cases for lists（lists用例）
（1）记录社交网络上用户最近一次更新，比如你的Twitter主页上想显示最近上传的照片，并且你想要快速的访问这些照片：
         1）每次用户上传一张照片后，我们将其id使用lpush存到list里；
         2）当用户访问主页时，我们使用lrange 0 9来获取最近10张照片；
（2）进程间通信，可以使用生产者-消费者模式，即生产者将元素放入list，消费者获得这些元素并执行相关操作，
         如ruby的resque和sidekiq类库底层使用redis list来实现backend job


Capped lists（封顶lists）
很多情况下我们想要使用list来存储最近的元素，无论是社交网络还是日志或者其他。
redis允许我们使用capped lists来仅仅存储我们想要的最近N个元素，并且丢弃老的元素。
（1）使用ltrim获取指定N个元素并舍弃其余元素
         命令：rpush mylist 1 2 3 4 5
                    lrange mylist 0 -1， 显示1，2，3，4，5
                    ltrim mylist 0 2
                    lrange mylist 0 -1， 显示1， 2， 3三个元素，4，5丢弃


Blocking operations on lists（lists的阻塞操作）
lists有个特殊的特点，可以使用它实现队列，通常可以作为进程间通信的媒介，实现阻塞操作。
比如实现生产者-消费者模式：
（1）生产者使用lpush将元素放入list
（2）消费者使用rpop从list中提出/处理元素
但是上面可能存在list为空的情况，即rpop返回nil，此时消费者需要等待并不断的重试rpop，即polling（轮询），此时的缺点是：
（1）强制redis client处理无用命令（命令return nil）
（2）在出现nil时，进程不断轮询等待，等待时间和轮询次数成反比
针对上面缺点，redis对rpop和lpop进一步封装，产生了brpop和blpop，使用bropo时，当list为nil时，block住，
只有当一个新的元素添加到list中后才会将结果返回给调用者，同时可以指定一个timeout时间，当超时后通知调用者
测试：需要打开2个redis-cli窗口
（1）窗口1执行（消费者）：lpush mylist 1 和 rpop mylist将mylist清空，再执行brpop mylist 20，设置20s等待，
                            或者执行brpop mylist 0，设置无限等待
（2）窗口2执行（生产者）：lpush mylist 2，此时窗口1会返回list名字和弹出的value 2，恰好是窗口2刚放入的元素
我们同样可以设置等待多个list，即只要其中有一个list有返回，就会结束等待，注意：
（1）消费者（redis客户端）会按顺序执行brpop命令并同时等待生产者放入元素，当生产者放入元素后，第一个执行brpop命令
         的消费者会首先获取元素，然后依次类推。（可以开启多个redis-cli测试，模拟多个消费者同时等待生产者的场景）
（2）brpop的返回和rpop的返回不一样，brpop返回一个array，且数组第一个元素是list的名字，第二个才是弹出的value
（3）如果超时，会返回nil给消费者
（4）可以使用rpoplpush构建安全队列或者轮换队列，参考：https://redis.io/commands/rpoplpush
（5）blocking的一种变体brpoplpush，参考：https://redis.io/commands/brpoplpush


Automatic creation and removal of keys（keys的原子创建和删除）
总结redis keys创建和删除的3种行为：
（1）当我们向聚合数据类型中添加一个元素时（比如向list中），如果key不存在，redis会默认创建一个key并将元素放入
         del mylist
         lpush mylist 1 2 3， 重新生成mylist

         set foo bar
         lpush foo 1 2 3， 类型不一致报错
         type foo，  返回string
（2）当我们从聚合数据类型中删除一个元素时（如从list删除），如果value为空，key会自动销毁（stream类型例外）
         lpush mylist 1 2 3
         exists mylist，  返回1
         lpop mylist
         lpop mylist
         lpop mylist
         exists mylist，  返回0，此时key销毁
（3）调用只读命令如llen（返回list长度）去操作一个已经被删除的list的key，返回为0
         del mylist
         llen mylist， 返回integer类型0
         lpop mylist， 返回nil


备注：
1. redis使用链表这个数据结构的另一个优点看英文描述不是很理解，原文如下：
Redis Lists can be taken at constant length in constant time.
constant意思通常是指常量，这里百度翻译是：Redis列表可以在固定时间内以固定长度获取

2. key的原子创建和删除的rule3看英文描述不是很理解，原文如下：
Calling a read-only command such as LLEN (which returns the length of the list), 
or a write command removing elements,  with an empty key, always produces the 
same result as if the key is holding an empty aggregate type of the type the command expects to find.
```
