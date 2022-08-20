---
title: 简单了解Zookeeper
date: 2022-01-27 14:34:43
tags: zookeeper
categories:
- [学习, 中间件学习, Zookeeper]
---

> 划重点： 分布式协调服务、ZAB协议、CAP定理之CP（leader选举期间失去A）

> 这里单机开启3个zk进程模拟集群环境

- zookeeper3.7.0
- jdk1.8
- maven3.8

# 安装zk
```bash
[root@localhost ~]# cd /home/jalen/zk
[root@localhost zk]# wget https://dlcdn.apache.org/zookeeper/zookeeper-3.7.0/apache-zookeeper-3.7.0-bin.tar.gz
[root@localhost zk]# tar -zxvf ./apache-zookeeper-3.7.0-bin.tar.gz
[root@localhost zk]# mv apache-zookeeper-3.7.0-bin
[root@localhost zk]# mv apache-zookeeper-3.7.0-bin.tar.gz bak/
[root@localhost zk]# cd apache-zookeeper-3.7.0-bin/conf
```

# 配置zk

## 创建配置文件
```bash
[root@localhost conf]# touch zoo1.cfg
[root@localhost conf]# touch zoo2.cfg
[root@localhost conf]# touch zoo3.cfg
```

## 添加内容
zoo1.cfg, zoo2.cfg, zoo3.cfg配置内容如下

```conf
# zoo1.cfg
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/tmp/zk1/data
dataLogDir=/tmp/zk1/log
clientPort=2181
server.1=localhost:2888:3888
server.2=localhost:2899:3899
server.3=localhost:2877:3877

# zoo2.cfg
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/tmp/zk2/data
dataLogDir=/tmp/zk2/log
clientPort=2182
server.1=localhost:2888:3888
server.2=localhost:2899:3899
server.3=localhost:2877:3877

# zoo3.cfg
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/tmp/zk3/data
dataLogDir=/tmp/zk3/log
clientPort=2183
server.1=localhost:2888:3888
server.2=localhost:2899:3899
server.3=localhost:2877:3877
```

配置参数说明：
- tickTime：  用于计算的基础时间单元。比如 session 超时：N*tickTime；
- initLimit： 用于集群，允许从节点连接并同步到 master 节点的初始化连接时间，以 tickTime 的倍数来表示；
- syncLimit： 用于集群， master 主节点与从节点之间发送消息，请求和应答时间长度（心跳机制）；
- dataDir：   数据存储位置；
- dataLogDir：日志目录；
- clientPort：用于客户端连接的端口，默认2181
- server.1： 表示编号为 1 的服务器，这里的 1 要与该服务器的 myid 文件的内容相同，可以是任意有效数字，标识这是第几个服务器节点；
- localhost： 正常是一个IP地址，指向该服务器的地址；
- 2888： 服务之间通信的端口，默认值 2888；
- 3888： 服务之间选举的端口，默认值 3888。

## 创建节点标识文件
这里写入1,2,3作为唯一标识，唯一标识可自定义。
```bash
[root@localhost conf]# mkdir -p /tmp/zk1/data && touch myid | echo 1 > /tmp/zk1/data/myid
[root@localhost conf]# mkdir -p /tmp/zk2/data && touch myid | echo 2 > /tmp/zk2/data/myid
[root@localhost conf]# mkdir -p /tmp/zk3/data && touch myid | echo 3 > /tmp/zk3/data/myid
```

# 启动zk
```bash
[root@localhost apache-zookeeper-3.7.0-bin]# ./bin/zkServer.sh start ./conf/zoo1.cfg &
[root@localhost apache-zookeeper-3.7.0-bin]# ./bin/zkServer.sh start ./conf/zoo2.cfg &
[root@localhost apache-zookeeper-3.7.0-bin]# ./bin/zkServer.sh start ./conf/zoo3.cfg &
```

# 检查zk
```bash
[root@localhost apache-zookeeper-3.7.0-bin]# ./bin/zkServer.sh status conf/zoo1.cfg
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: conf/zoo1.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: follower
[root@localhost apache-zookeeper-3.7.0-bin]# ./bin/zkServer.sh status conf/zoo2.cfg
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: conf/zoo2.cfg
Client port found: 2182. Client address: localhost. Client SSL: false.
Mode: leader
[root@localhost apache-zookeeper-3.7.0-bin]# ./bin/zkServer.sh status conf/zoo3.cfg
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: conf/zoo3.cfg
Client port found: 2183. Client address: localhost. Client SSL: false.
Mode: follower
[root@localhost apache-zookeeper-3.7.0-bin]# 
```

zk启动后触发第一轮选举，从上面可以看到zoo2是leader，zoo1和zoo3是follower，此时zk集群启动完毕。

# 可视化zkui（可选）

## 安装配置zkui

首先需要jdk和maven，尽量不要用openjdk，maven主要用来build jar包。

```bash
[root@localhost jalen]# mkdir jdk
[root@localhost jalen]# cd jdk
[root@localhost jdk]# wget https://download.oracle.com/otn/java/jdk/8u321-b07/df5ad55fdd604472a86a45a217032c7d/jdk-8u321-linux-x64.tar.gz?AuthParam=xxx
[root@localhost jdk]# tar -zxvf 'jdk-8u321-linux-x64.tar.gz?AuthParam=xxx'
[root@localhost jdk]# cd jdk1.8.0_321/
[root@localhost jdk1.8.0_321]# pwd  # /home/jalen/jdk/jdk1.8.0_321


[root@localhost jalen]# mkdir maven
[root@localhost jalen]# cd maven/
[root@localhost maven]# wget https://dlcdn.apache.org/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.tar.gz
[root@localhost maven]# tar -zxvf apache-maven-3.8.4-bin.tar.gz
[root@localhost apache-maven-3.8.4]# pwd   # /home/jalen/maven/apache-maven-3.8.4
[root@localhost apache-maven-3.8.4]# vi /etc/profile
export JAVA_HOME=/home/jalen/jdk/jdk1.8.0_321
export M2_HOME=/home/jalen/maven/apache-maven-3.8.4
export MAVEN_HOME=/home/jalen/maven/apache-maven-3.8.4
export PATH=${JAVA_HOME}/bin:${M2_HOME}/bin:${PATH}

[root@localhost apache-maven-3.8.4]# source /etc/profile
[root@localhost apache-maven-3.8.4]# mvn -v
[root@localhost apache-maven-3.8.4]# java -version

[root@localhost jalen]# mkdir zkui
[root@localhost jalen]# cd zkui/
[root@localhost zkui]# git clone https://github.com/DeemOpen/zkui.git
[root@localhost zkui]# cd zkui/
[root@localhost zkui]# mvn clean install

[root@localhost zkui]# vi config.cfg  # 适当修改
[root@localhost zkui]# nohup java -jar target/zkui-2.0-SNAPSHOT-jar-with-dependencies.jar &  # Access： http://192.168.189.128:9090/  登录用户名密码可以看下config.cfg
```

## zkui首页报错解决

访问zkui页面报：KeeperErrorCode = NoNode for /appconfig/hosts
```bash
[root@localhost bin]# ./zkCli.sh -server 192.168.189.128:2181
WATCHER::

WatchedEvent state:SyncConnected type:None path:null
ls /
[1111, zookeeper]
[zk: 192.168.189.128:2181(CONNECTED) 1] create /appconfig "my appconfig"
Created /appconfig
[zk: 192.168.189.128:2181(CONNECTED) 2] create /appconfig/hosts 192.168.189.128
Created /appconfig/hosts
[zk: 192.168.189.128:2181(CONNECTED) 4] ls /
[1111, appconfig, zookeeper]
[zk: 192.168.189.128:2181(CONNECTED) 5] 
```

# 了解zk

## 用途

- 服务注册与发现：区别于eureka（eureka满足CAP种的AP）- （使用临时节点）
- 分布式锁：区别于redis（redis原子性，自旋锁等）- （使用临时顺序节点）
- 配置管理：（使用持久节点）

## Znode

Znode是zookeeper的数据节点模型，分为持久节点、持久顺序节点、临时节点、临时顺序节点四种，每个Znode由下面4部分组成：

- data ： Znode 存储的数据信息，这里的数据指用户保存的数据，最大不能超过1M；
- ACL ： 对节点进行权限控制，记录了哪些用户或者哪些 IP 地址可以访问本节点；
- child ： 当前节点的子节点引用，通过这个节点来找到它的子节点；
- stat ： 包含 Znode 的各种元数据，比如事务 ID、版本号、时间戳、大小等 Znode 本身的数据。

## zkCli操作
注意：操作的节点必须是全路径
```bash
[root@localhost bin]# ./zkCli.sh -server 192.168.189.128:2181
[zk: 192.168.189.128:2181(CONNECTED) 0] create /persistent_node  # 创建一个持久节点
Created /persistent_node
[zk: 192.168.189.128:2181(CONNECTED) 1] create -s /persistent_sequential_node  # 创建一个持久顺序节点
Created /persistent_sequential_node0000000003
[zk: 192.168.189.128:2181(CONNECTED) 4] create -e /ephemeral_node  # 创建一个临时节点
Created /ephemeral_node
[zk: 192.168.189.128:2181(CONNECTED) 5] create -s -e /ephemeral_sequential_node  # 创建一个临时顺序节点
Created /ephemeral_sequential_node0000000006

[zk: 192.168.189.128:2181(CONNECTED) 9] create /jalen_test_node  # 创建节点
Created /jalen_test_node
[zk: 192.168.189.128:2181(CONNECTED) 10] delete /jalen_test_node  # 删除节点

[zk: 192.168.189.128:2181(CONNECTED) 17] delete /1111/foo  
[zk: 192.168.189.128:2181(CONNECTED) 18] delete /1111  # 删除父节点需要先删除父节点下所有子节点

[zk: 192.168.189.128:2181(CONNECTED) 13] set /persistent_node jalen  # 设置节点数据
[zk: 192.168.189.128:2181(CONNECTED) 14] get /persistent_node  # 获取节点数据
jalen

[zk: 192.168.189.128:2181(CONNECTED) 20] ls /  # 查看节点
[appconfig, ephemeral_node, ephemeral_sequential_node0000000006, persistent_node, persistent_sequential_node0000000003, zookeeper]
[zk: 192.168.189.128:2181(CONNECTED) 21] quit  # 退出

[root@localhost bin]# ./zkCli.sh -server 192.168.189.128:2182
[zk: 192.168.189.128:2182(CONNECTED) 0] ls /
[appconfig, persistent_node, persistent_sequential_node0000000003, zookeeper]
[zk: 192.168.189.128:2182(CONNECTED) 1] get /persistent_node  # 验证集群内节点间数据同步
jalen
```

## 选举

1. 服务初次启动时的 Leader 选举 

   所有 Zookeeper 服务的状态都会变为 Looking 选举状态并把自己当作 Leader 候选者向其它 Zookeeper 服务发送自己的选票信息，选票信息包括事务ID（64bit）+myid，正常情况下，在第一轮的选举中，每个 Zookeeper 服务的选票信息都是自身的信息，所以不会产生 Leader，此时就会开启第二轮选举。第二轮产生新的选票信息，如果超过半数的 Zookeeper 服务的选票信息是相同的就当选为 Leader 服务，其状态变为 Leading，其他变为Following。

2. 崩溃恢复时的 Leader 选举

   根据ZAB协议，Leader 节点发生故障，集群暂停对外服务，进入崩溃恢复。如果此时Leader 节点恢复，加入集群，也会进入Looking 状态，参与选举。分为Leader election、Discovery、Synchronization三个阶段。第一阶段先选出leader，第二阶段确认leader数据最新，第三阶段将leader数据同步到follower，半数follower同步成功后，此leader才会称为真正的leader，后两个阶段也称为“消息广播”，所以也只能有一个 Leader 进行广播。

## ZAB

ZAB（Zookeeper Atomic Broadcast ，原子消息广播协议）协议主要用来保证一致性。

2种模式：
- 崩溃恢复
- 消息广播

3种节点状态： 
- Looking：选举状态；
- Following：从节点所处的状态；
- Leading：主节点所处的状态。

## 广播、事务
zk客户端向zk集群某个follower发request，如执行增删节点操作，此时该follower不会自己处理，会将request转发给leader，让leader处理事务request，leader接收后，生成proposal提案广播到所有follower，follower收到leader的proposal后，会ack一下leader表明自己也能够处理该事务，leader收到半数以上follower的ack后，会向集群广播commit信息，通知follower提交事务，同时自己也提交该事务，如果有一小部分follower没有ack leader怎么办呢？此时leader会把它们从follower列表移除掉。

## Observer
Observer是zk3.3.0版本后新功能，主要用来提高 Zookeeper 集群的非事务请求的效率，主要针对查询。Observer 作为观察者在 Zookeeper 集群中观察 Zookeeper 集群的最新的数据变化，然后从 Leader 节点获取最新变化的数据并同步到自身（leader处理事务后会通知Observer同步），然后再向外提供服务。这里不需要proposal，Observer节点也不参与选举。zoo.cfg配置如下：
```conf
# 声明此服务为 Observer 节点
peerType=observer

# 添加 observer 标识，声明 server.1 为 Observer 节点
server.1:192.168.0.77:2181:3181:observer
```

## 实现分布式锁
memcached可以通过重复执行`add`操作判断锁占用，redis可以重复使用`setnx`判断锁占用，zk可以使用临时顺序节点的特性实现分布式锁和锁的等待队列。

zk临时顺序节点特性：客户端断开后znode消失，每次生成znode会生成一个顺序编号（可以根据大小判断先后，顺序编号最小的获得锁，最大的则获得锁失败）

释放锁两种方式：1.主动执行删除操作，2.关闭客户端，结束session会话

## 实现分布式ID
| 方法 | 特征 | 优点 | 缺点 |
| ---- | ----|---- |  ---- |
| uuid|Universally Unique Identifier,32 个字符组成，采用 16 进制进行编码,定义了在时间和空间都完全唯一的系统信息| 简单，本地生成，速度快，不依赖网络和其他服务| 无法识别，没有顺序性
| redis|使用incr自增命令|简单，有序递增|依赖redis，需要保证redis的HA，网络传输问题
|雪花算法|Twitter SnowFlake,由64 位 Long 型的 ID，它是由 1 位符号位，41 位的时间戳毫秒数，10 位的机器 ID，12 位的序列号这 4 种元素来组成。理论上，雪花算法每秒可以生成 400 多万个 ID。|高性能，灵活调整,不需要第三方组件  | 依赖机器时钟，时钟回拨问题
| zk|利用持久顺序节点特性，截取后面顺序编号 |HA,趋势递增|性能差，定期删除节点麻烦

## 实现负载均衡
负载均衡策略：Round Robin 轮询，Random 随机，Consistent Hashing 一致性哈希，加权轮询，加权随机，Least Connection 最小连接数。

这里可以使用zk的临时节点维护路由ip列表

## 实现配置中心
正常配置中心的实现有2种方式，要么服务端定时去配置中心拉取配置，要么配置中心主动推送配置。

zk的实现方式是：create znode，设置data，添加watch监听，znode变化推送给服务，服务回调zk取数据。

## 实现集群管理
监控集群中服务运行状态，操作集群中的服务上下线。

服务启动时将自身信息注册到zk的临时节点，服务断开或下线时临时节点会自动移除。也可以通过手动操作api执行下线操作。

## 基于zk的实现
Apache Kafka、Apache Dubbo等



# 附录

## CAP定理：任何分布式系统都只能保证其中两条。

C ： Consistent ，一致性，需要保证数据的一致性。
A ： Availability ，可用性，需要保证服务的可用性。
P ： Partition tolerance ，分区容错性，服务对网络分区故障的容错性。

## 为什么集群节点数是奇数呢？

如果采用偶数，在 Leader 节点选举投票时，有可能会产生两个 Leader 节点，两个 Leader 都不能满足大多数选票的原则，这时就会出现脑裂问题。类似有ES集群等等。


# 参考

https://blog.csdn.net/hacker_Lees/article/details/104989284

https://www.cnblogs.com/iforever/p/9095095.html

https://linuxize.com/post/how-to-install-apache-maven-on-centos-7/

https://github.com/DeemOpen/zkui

https://zhuanlan.zhihu.com/p/33999708  （CAP理论）

https://www.imooc.com/wiki/Zookeeper/zookeepercurator.html

https://kazoo.readthedocs.io/en/latest/basic_usage.html（python）
