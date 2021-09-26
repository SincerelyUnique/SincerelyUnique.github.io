---
title: es集群脑裂问题
date: 2021-02-25 12:05:17
tags:
- elasticsearch
categories:
---

第一次弄这个，可能下面的描述有些问题，仅供参考，尽量去看官方文档和下面列出来的链接。

1.脑裂问题（split brain issues）

ref：https://qbox.io/blog/split-brain-problem-elasticsearch
当节点崩溃或由于某种原因而导致节点之间的通信中断时，就会出现问题。如果一个从属节点
无法与主节点进行通信，则它会从仍与之连接的主节点开始选举一个新的主节点。然后，该新
的主节点将接管先前的主节点的职责。如果旧的主节点重新加入集群或恢复了通信，则新的主
节点会将其降级为从节点，因此不会发生冲突。在大多数情况下，此过程是无缝的，并且“有效”。

但是，如果考虑只有两个节点的情况：一个主节点和一个从节点。如果两者之间的通信中断，
slave将会提升为master，但是一旦恢复通信，将会拥有两个master节点。最初的master主节点
认为从节点掉线，应该重新作为从节点，而新主节点认为最初的主节点掉线，应该作为从节点。
此时会产生集群的脑裂问题。（个人测试没有复现出两个节点的脑裂问题，可能没有深入测试的
原因，我这边两个节点中从节点挂掉后，主节点可正常执行修改/查询操作，但是主节点挂掉
后，因为从节点也是一个数据节点，还是可以查询的，但是不能做修改操作，因为使用的是
python es插件从code层面做的操作，可能插件对于2个节点可能造成的脑裂问题做了保护机制，
导致code执行修改操作时候总会尝试连接挂掉的主节点，导致超时异常，操作期间数据同步正常）

看另外一个博客有描述：假设拥有一个10个节点组成的集群，有3个节点从集群中断开连接，
由于发现机制，这3个节点可能会组成一个新的集群，这样就产生了两个同名的集群，这就是脑
分裂（split-brain)，此时需要设置discovery.zen.minium_master_nodes:6 （这个节点太多了，
没有真实测试，不过看描述是可以理解的，下面有尝试4个节点3个法定人数，即3个quorum）


官方论坛有一个讨论2个节点脑裂问题的帖子，可以参考下，To avoid split brain issues.
ref：https://discuss.elastic.co/t/how-can-a-configure-two-node-in-one-cluster/171088/11
帖子中有位es team的成员说：That's the recommandation to avoid split brain issues.
All 3 nodes must be master elligible nodes. Basically. 并列出了3个节点的配置信息
所以建议最少配置3个节点，当然节点越多，HA可能会更好，具体还是看实际业务情况以及可利用
的服务器资源。


es官方文档中也有两个节点集群的描述，但是有重点指出：Because it’s not resilient to failures, 
we do not recommend deploying a two-node cluster in production. 所以也是不建议在生产环境
搞两个节点的。 

我在测试两个节点的故障转移时，即做以下简单配置后

discovery.seed_hosts: ["ip1", "ip2"]
cluster.initial_master_nodes: ["ip1", "ip2"]

发现：

如果从节点挂掉：剩下一台主节点，es日志可以看到从节点离开（left）且无异常信息抛出，并且对于code这边的修改操作和查询操作依然都可以正常完成

如果主节点挂掉：剩下一台从节点，es日志会报connect error，并一直尝试触发选举，但是选举时一直尝试访问主节点（参考下面log）， 对于code这边的查询操作可以正常完成，但无法执行修改操作，如果从code这边执行修改操作会报连接主节点timeout异常， （我这里想达到和redis主从复制的效果-即主节点挂掉后从节点自动upgrade为主，但是实际来看却达不到，因为从节点一直在尝试ping主节点从而产生connect error异常）
                
主节点挂掉后从节点日志：master not discovered or elected yet, an election requires a node with id [主节点ID]，... have discovered [从节点] which is not a quorum; discovery will continue using [主节点]

ref：https://www.elastic.co/guide/en/elasticsearch/reference/current/high-availability-cluster-small-clusters.html#high-availability-cluster-design-two-nodes


2.es内置自动发现机制：zen

ref：https://www.elastic.co/guide/en/elasticsearch/reference/6.8/modules-discovery-zen.html
只需要配置相同的cluster.name就能将节点加入同一集群


4个节点集群

```raw
cluster.name: my-application
node.name: node-1
path.data: /opt/es/elasticsearch-7.6.1/data
path.logs: /opt/es/elasticsearch-7.6.1/log
network.host: ip1
http.port: 9200
discovery.seed_hosts: ["ip1", "ip2", "ip3", "ip4"]
cluster.initial_master_nodes: ["ip1", "ip2", "ip3", "ip4"]
bootstrap.system_call_filter: false
bootstrap.memory_lock: false
http.cors.enabled: true
http.cors.allow-origin: "*"
discovery.zen.minimum_master_nodes: 3
 
cluster.name: my-application
node.name: node-2
path.data: /opt/es/elasticsearch-7.6.1/data
path.logs: /opt/es/elasticsearch-7.6.1/log
network.host: ip2
http.port: 9200
discovery.seed_hosts: ["ip1", "ip2", "ip3", "ip4"]
cluster.initial_master_nodes: ["ip1", "ip2", "ip3", "ip4"]
bootstrap.system_call_filter: false
bootstrap.memory_lock: false
http.cors.enabled: true
http.cors.allow-origin: "*"
discovery.zen.minimum_master_nodes: 3
 
cluster.name: my-application
node.name: node-3
path.data: /opt/es/elasticsearch-7.6.1/data
path.logs: /opt/es/elasticsearch-7.6.1/log
network.host: ip3
http.port: 9200
discovery.seed_hosts: ["ip1", "ip2", "ip3", "ip4"]
cluster.initial_master_nodes: ["ip1", "ip2", "ip3", "ip4"]
bootstrap.system_call_filter: false
bootstrap.memory_lock: false
http.cors.enabled: true
http.cors.allow-origin: "*"
discovery.zen.minimum_master_nodes: 3
 
cluster.name: my-application
node.name: node-4
path.data: /opt/es/elasticsearch-7.6.1/data
path.logs: /opt/es/elasticsearch-7.6.1/log
network.host: ip4
http.port: 9200
discovery.seed_hosts: ["ip1", "ip2", "ip3", "ip4"]
cluster.initial_master_nodes: ["ip1", "ip2", "ip3", "ip4"]
bootstrap.system_call_filter: false
bootstrap.memory_lock: false
http.cors.enabled: true
http.cors.allow-origin: "*"
discovery.zen.minimum_master_nodes: 3
```

检查某个节点健康状态

访问：http://ip:9200/_cluster/health

返回：

```raw
{
    "cluster_name": "my-application",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 4,
    "number_of_data_nodes": 4,
    "active_primary_shards": 6,
    "active_shards": 12,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "task_max_waiting_in_queue_millis": 0,
    "active_shards_percent_as_number": 100
}
```
