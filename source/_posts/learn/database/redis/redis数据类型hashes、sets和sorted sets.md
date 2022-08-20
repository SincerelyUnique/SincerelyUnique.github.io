---
title: redis数据类型hashes、sets和sorted sets
date: 2020-07-14 18:50:46
tags:
- redis
categories:
- [学习, 数据库学习, Redis]
---

```
redis hashes（参考：https://redis.io/topics/data-types-intro#hashes）
放入hash表中的字段数没有实际限制；
同时一些小的hashes在内存中被以一种特殊的方式进行编码，以达到内存的高效使用；
hashes命令查询地址：https://redis.io/commands#hash
命令及使用如下：
（1）hmset可以设置多个hash属性： hmset <object:id> key1 value1 key2 value2 key3 value3 ...
         eg：hmset user:1000 username antirez birthyear 1977 verified 1
（2）hget可以获取其中某一个属性： hget <object:id> key1
         eg：hget user:1000 username
（3）hgetall可以获取包含field-value的一个array： hgetall <object:id>
         eg：hgetall user:1000
（4）hmget可以获取所有属性的值，返回一个数组：hmget <object:id> key1 key2 ...
         eg：hmget user:1000 username birthyear no-such-field
（5）让对象中某个属性自增
         eg：hincrby user:1000 birthyear 10, 此时由1977变为1987


redis sets
redis sets是唯一、无序的string集合；
set适合表示对象之间的关系，例如我们可以使用set集合来实现标签功能；
set也可以实现扑克牌发牌的功能；
命令及使用如下：
（1）sadd可以添加一个新的元素到set集合中
         eg：sadd myset 1 2 3
（2）smembers可以查询set中元素，因为无序，所以每次查询顺序可能不一致
         eg：smembers myset
（3）测试元素是否存在，返回1表示已存在，返回0表示不存在
         eg：sismember myset 3
                 sismember myset 300
（4）实现标签功能，即我们将每个要标记的对象设置一个集合，该集合用来存储与对象关联的标签ID
         eg：sadd news:1000:tags 1 2 5 77,   article ID 1000 有4个标签（1，2，5，77）
         或者反过来可以设置1，2，5，77四个标签对应article ID为1000
         eg：sadd tag:1:news 1000
                 sadd tag:2:news 1000
                 sadd tag:5:news 1000
                 sadd tag:77:news 1000
         获取所有标签
         eg：smembers news:1000:tags
（5）使用sinter求集合交集
         eg：sinter tag:1:news tag:2:news tag:5:news tag:77:news
（6）使用sunion求集合并集
         eg：sunion tag:1:news tag:2:news tag:5:news tag:77:news
（7）使用sdiff求集合差集
         eg：sdiff tag:2:news tag:1:news
（8）实现扑克牌发牌功能（(C)lubs, (D)iamonds, (H)earts, (S)pades）
         1）使用sadd定义一副扑克牌
         2）使用sunionstore为扑克牌创建一个副本，当第一局结束后可以直接从该副本中取一副新牌
         3）此时打开3个redis-cli窗口，模拟3人斗地主，系统使用spop每人发5张牌
         4）发牌时可以使用scard统计剩余扑克牌的数量（通常称为一个set集合的cardinality基数）
         eg：sadd deck C1 C2 C3 C4 C5 C6 C7 C8 C9 C10 CJ CQ CK
                                   D1 D2 D3 D4 D5 D6 D7 D8 D9 D10 DJ DQ DK 
                                   H1 H2 H3 H4 H5 H6 H7 H8 H9 H10 HJ HQ HK 
                                   S1 S2 S3 S4 S5 S6 S7 S8 S9 S10 SJ SQ SK
                 sunionstore game:1:deck deck
                 spop game:1:deck
                 spop game:1:deck
                 spop game:1:deck
                 spop game:1:deck
                 spop game:1:deck
                 scard game:1:deck
（9）如果我们不想从原来的set集合中删除元素，仅希望能够每次从set集合中取随机的一个元素，
         可以使用srandmember命令，它还具有返回重复元素和非重复元素的功能。


redis sorted sets
sorted sets有点像set和hash的混合；
sorted sets每个元素关联一个浮点类型的value，名字叫score（这就是为什么像hash，一个element映射一个value）
sorted sets集合中的元素是按顺序排列的，遵循以下规则：
（1）如果A和B是2个元素，如果A的score大于B的score，那么A大于B
（2）如果A和B有相同的score，那么比较A字符串在字典上是否大于B字符串，如果大于B，则A大于B
sorted sets集合可以做范围查询，如取前10个，后10个
sorted sets允许我们将排序后的集合用作通用的索引（the 128 bit number in big endian）
redis sorted sets zrangebylex demo：http://autocomplete.redis.io/
可以使用score做排行榜，如facebook小游戏；
命令及使用如下：
（1）向sorted sets中添加hacker名字，每个hacker名字前面就是他的score，当我们每次添加后，他们其实已经按年龄排序了
         zadd hackers 1940 "Alan Kay"
         zadd hackers 1957 "Sophie Wilson"
         zadd hackers 1953 "Richard Stallman"
         zadd hackers 1949 "Anita Borg"
         zadd hackers 1965 "Yukihiro Matsumoto"
         zadd hackers 1914 "Hedy Lamarr"
         zadd hackers 1916 "Claude Shannon"
         zadd hackers 1969 "Linus Torvalds"
         zadd hackers 1912 "Alan Turing"
（2）查询sorted sets值，可见已排序
         zrange hackers 0 -1
（3）如果想倒序排列，使用zrevrange
         zrevrange hackers 0 -1
（4）如果想要同时输出score的值，可以加withscores参数
         zrange hackers 0 -1 withscores
（5）查询1950年前的hackers，如下返回年龄在负无穷大到1950年之间的hacker的闭集
         zrangebyscore hackers -inf 1950
（6）移除所有在1940年到1960年之间的hackers，返回删除的数量
         zremrangebyscore hackers 1940 1960
（7）get-rank操作，查看元素在有序元素集合中的位置
         zrank hackers "Anita Borg"
（8）如果将所有元素的score都定义为0，此时会使用字典（lexicographically）排序，即ABCD...，
         此时可以使用zrangebylex实现范围查询，如查询B-P之间的元素
         zadd hackers 0 "Alan Kay" 0 "Sophie Wilson" 0 "Richard Stallman"
                                0 "Anita Borg" 0 "Yukihiro Matsumoto" 0 "Hedy Lamarr"
                                0 "Claude Shannon" 0 "Linus Torvalds" 0 "Alan Turing"
         zrange hackers 0 -1
         zrangebylex hackers [B [P
（9）使用zadd更新一个已存在的value对应的score
         zadd hackers 1980 "Richard Stallman"
         zrange hackers 0 -1
```
