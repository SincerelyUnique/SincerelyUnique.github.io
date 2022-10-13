---
title: ES bulk数据时断路器异常
date: 2022-10-13 00:31:49
tags:
- elasticsearch
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> ES Bulk Exception - circuit breaker errors

# 程序报错信息

```raw
2022-10-08 06:39:22,109 7093:139831270405888 xxxx.py:112:INFO:Traceback (most recent call last):
  File "/users/jalchu/venv/lib/python3.6/site-packages/elasticsearch/helpers/actions.py", line 390, in bulk
    for ok, item in streaming_bulk(client, actions, *args, **kwargs):
  File "/users/jalchu/venv/lib/python3.6/site-packages/elasticsearch/helpers/actions.py", line 320, in streaming_bulk
    **kwargs
  File "/users/jalchu/venv/lib/python3.6/site-packages/elasticsearch/helpers/actions.py", line 247, in _process_bulk_chunk
    for item in gen:
  File "/users/jalchu/venv/lib/python3.6/site-packages/elasticsearch/helpers/actions.py", line 188, in _process_bulk_chunk_success
    raise BulkIndexError("%i document(s) failed to index." % len(errors), errors)
elasticsearch.helpers.errors.BulkIndexError: ('1 document(s) failed to index.', [{'index': {'_index': 'xxxx_index', '_type': '_doc', '_id': '6105733_8f4f8914-2437-46c7-a438-ec599ceca207', 'status': 429, 'error': {'type': 'circuit_breaking_exception', 'reason': '[parent] Data too large, data for [<transport_request>] would be [2123952938/1.9gb], which is larger than the limit of [2040109465/1.8gb], real usage: [2123950800/1.9gb], new bytes reserved: [2138/2kb], usages [request=0/0b, fielddata=7712/7.5kb, in_flight_requests=2138/2kb, accounting=1422929/1.3mb]'
```

# 参考官网给出的说明

https://www.elastic.co/guide/en/elasticsearch/reference/7.14/fix-common-cluster-issues.html#circuit-breaker-errors

# 查询ES集群中各节点堆使用信息

ES提供了两个API可以实时查看当前ES集群中各个节点的堆信息，我们可以从kibana页面进入Dev Tools，执行下面命令。

1. cat nodes API 

  - API地址：https://www.elastic.co/guide/en/elasticsearch/reference/7.14/cat-nodes.html

  - API请求：

    ```
    GET _cat/nodes?v=true&h=name,node*,heap*
    GET /_cat/nodes?v=true&h=id,ip,port,v,m,node*,heap*
    ```

  - 返回：能够看到各节点堆信息，主要看下`heap.percent`：

    ```bash
    name   id   node.role heap.current heap.percent heap.max
    node-1 54jZ dilm             1.6gb           84      2gb
    node-3 goAZ dilm           816.8mb           39      2gb
    node-2 5NzV dilm           735.9mb           35      2gb
    node-4 Gzzg dilm           673.7mb           32      2gb
    ```

2. node stats API

   - 说明：用来获取每个circuit breaker的jvm memory usage
   
   - API地址：https://www.elastic.co/guide/en/elasticsearch/reference/7.14/cluster-nodes-stats.html

   - API请求：
   
   ```
   GET _nodes/stats/breaker
   GET _nodes/stats?filter_path=nodes.*.jvm.mem.pools.old
   ```

   - 返回：

    ```json
    {
      "_nodes" : {
        "total" : 4,
        "successful" : 4,
        "failed" : 0
      },
      "cluster_name" : "my-application",
      "nodes" : {
        "54jZKQglTz-L9DhUz7MCTQ" : {
          "timestamp" : 1665542452623,
          "name" : "node-1",
          "transport_address" : "xxxx:9300",
          "host" : "xxxx",
          "ip" : "xxxx:9300",
          "roles" : [
            "ingest",
            "master",
            "data",
            "ml"
          ],
          "attributes" : {
            "ml.machine_memory" : "33566990336",
            "ml.max_open_jobs" : "20",
            "xpack.installed" : "true"
          },
          "breakers" : {
            "request" : {
              "limit_size_in_bytes" : 1288490188,
              "limit_size" : "1.1gb",
              "estimated_size_in_bytes" : 0,
              "estimated_size" : "0b",
              "overhead" : 1.0,
              "tripped" : 0
            },
            "fielddata" : {
              "limit_size_in_bytes" : 858993459,
              "limit_size" : "819.1mb",
              "estimated_size_in_bytes" : 6648,
              "estimated_size" : "6.4kb",
              "overhead" : 1.03,
              "tripped" : 0
            },
            "in_flight_requests" : {
              "limit_size_in_bytes" : 2147483648,
              "limit_size" : "2gb",
              "estimated_size_in_bytes" : 1176,
              "estimated_size" : "1.1kb",
              "overhead" : 2.0,
              "tripped" : 0
            },
            "accounting" : {
              "limit_size_in_bytes" : 2147483648,
              "limit_size" : "2gb",
              "estimated_size_in_bytes" : 1136588,
              "estimated_size" : "1mb",
              "overhead" : 1.0,
              "tripped" : 0
            },
            "parent" : {
              "limit_size_in_bytes" : 2040109465,
              "limit_size" : "1.8gb",
              "estimated_size_in_bytes" : 328488032,
              "estimated_size" : "313.2mb",
              "overhead" : 1.0,
              "tripped" : 734
            }
          }
        },
        "goAZ-YvVS5qOCXCe_ifi6g" : {
          "timestamp" : 1665542452649,
          "name" : "node-3",
          "transport_address" : "xxxx:9300",
          "host" : "xxxx",
          "ip" : "xxxx:9300",
          "roles" : [
            "ingest",
            "master",
            "data",
            "ml"
          ],
          "attributes" : {
            "ml.machine_memory" : "16655998976",
            "ml.max_open_jobs" : "20",
            "xpack.installed" : "true"
          },
          "breakers" : {
            "request" : {
              "limit_size_in_bytes" : 1288490188,
              "limit_size" : "1.1gb",
              "estimated_size_in_bytes" : 0,
              "estimated_size" : "0b",
              "overhead" : 1.0,
              "tripped" : 1
            },
            "fielddata" : {
              "limit_size_in_bytes" : 858993459,
              "limit_size" : "819.1mb",
              "estimated_size_in_bytes" : 4712,
              "estimated_size" : "4.6kb",
              "overhead" : 1.03,
              "tripped" : 0
            },
            "in_flight_requests" : {
              "limit_size_in_bytes" : 2147483648,
              "limit_size" : "2gb",
              "estimated_size_in_bytes" : 1176,
              "estimated_size" : "1.1kb",
              "overhead" : 2.0,
              "tripped" : 0
            },
            "accounting" : {
              "limit_size_in_bytes" : 2147483648,
              "limit_size" : "2gb",
              "estimated_size_in_bytes" : 3777449,
              "estimated_size" : "3.6mb",
              "overhead" : 1.0,
              "tripped" : 0
            },
            "parent" : {
              "limit_size_in_bytes" : 2040109465,
              "limit_size" : "1.8gb",
              "estimated_size_in_bytes" : 687099680,
              "estimated_size" : "655.2mb",
              "overhead" : 1.0,
              "tripped" : 1172
            }
          }
        },
        "5NzVnffXRjaC54hkmCTo3g" : {
          "timestamp" : 1665542452644,
          "name" : "node-2",
          "transport_address" : "xxxx:9300",
          "host" : "xxxx",
          "ip" : "xxxx:9300",
          "roles" : [
            "ingest",
            "master",
            "data",
            "ml"
          ],
          "attributes" : {
            "ml.machine_memory" : "33566990336",
            "xpack.installed" : "true",
            "ml.max_open_jobs" : "20"
          },
          "breakers" : {
            "request" : {
              "limit_size_in_bytes" : 1288490188,
              "limit_size" : "1.1gb",
              "estimated_size_in_bytes" : 0,
              "estimated_size" : "0b",
              "overhead" : 1.0,
              "tripped" : 0
            },
            "fielddata" : {
              "limit_size_in_bytes" : 858993459,
              "limit_size" : "819.1mb",
              "estimated_size_in_bytes" : 4872,
              "estimated_size" : "4.7kb",
              "overhead" : 1.03,
              "tripped" : 0
            },
            "in_flight_requests" : {
              "limit_size_in_bytes" : 2147483648,
              "limit_size" : "2gb",
              "estimated_size_in_bytes" : 0,
              "estimated_size" : "0b",
              "overhead" : 2.0,
              "tripped" : 0
            },
            "accounting" : {
              "limit_size_in_bytes" : 2147483648,
              "limit_size" : "2gb",
              "estimated_size_in_bytes" : 1422122,
              "estimated_size" : "1.3mb",
              "overhead" : 1.0,
              "tripped" : 0
            },
            "parent" : {
              "limit_size_in_bytes" : 2040109465,
              "limit_size" : "1.8gb",
              "estimated_size_in_bytes" : 1707026432,
              "estimated_size" : "1.5gb",
              "overhead" : 1.0,
              "tripped" : 284
            }
          }
        },
        "GzzgYowlQyicQQZeN7ykQw" : {
          "timestamp" : 1665542452642,
          "name" : "node-4",
          "transport_address" : "xxxx:9300",
          "host" : "xxxx",
          "ip" : "xxxx:9300",
          "roles" : [
            "ingest",
            "master",
            "data",
            "ml"
          ],
          "attributes" : {
            "ml.machine_memory" : "16655998976",
            "ml.max_open_jobs" : "20",
            "xpack.installed" : "true"
          },
          "breakers" : {
            "request" : {
              "limit_size_in_bytes" : 1288490188,
              "limit_size" : "1.1gb",
              "estimated_size_in_bytes" : 0,
              "estimated_size" : "0b",
              "overhead" : 1.0,
              "tripped" : 0
            },
            "fielddata" : {
              "limit_size_in_bytes" : 858993459,
              "limit_size" : "819.1mb",
              "estimated_size_in_bytes" : 6080,
              "estimated_size" : "5.9kb",
              "overhead" : 1.03,
              "tripped" : 0
            },
            "in_flight_requests" : {
              "limit_size_in_bytes" : 2147483648,
              "limit_size" : "2gb",
              "estimated_size_in_bytes" : 1176,
              "estimated_size" : "1.1kb",
              "overhead" : 2.0,
              "tripped" : 0
            },
            "accounting" : {
              "limit_size_in_bytes" : 2147483648,
              "limit_size" : "2gb",
              "estimated_size_in_bytes" : 3537488,
              "estimated_size" : "3.3mb",
              "overhead" : 1.0,
              "tripped" : 0
            },
            "parent" : {
              "limit_size_in_bytes" : 2040109465,
              "limit_size" : "1.8gb",
              "estimated_size_in_bytes" : 652858992,
              "estimated_size" : "622.6mb",
              "overhead" : 1.0,
              "tripped" : 792
            }
          }
        }
      }
    }
    ```

# 减轻jvm内存压力

如果上述查询的节点中有节点内存使用率超过85%，ES认为该集群就是不健康的集群，就有可能会遇到[circuit breaker errors](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/fix-common-cluster-issues.html#circuit-breaker-errors)，所以我们要做一些处理。

## 诊断JVM高内存占用情况

使用ES API查询`GET _nodes/stats?filter_path=nodes.*.jvm.mem.pools.old`

> JVM Memory Pressure = used_in_bytes / max_in_bytes

随着内存使用增加，jvm 的gc会变得越来越频繁，gc事件可以从[elasticsearch.log](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/logging.html)里找到，例如，以下事件表明 Elasticsearch 在过去 40 秒中花费了超过 50%（21 秒）执行垃圾收集。

```log
[timestamp_short_interval_from_last][INFO ][o.e.m.j.JvmGcMonitorService] [node_id] [gc][number] overhead, spent [21s] collecting in the last [40s]
```

## 减轻jvm内存压力

1. 减少shard数量，可以设置少量的分片，每个分片占用较大的内存，这样比多分片小内存划得来
2. 避免昂贵的查询，这些查询会占用更多内存，可以参考[slow log](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/index-modules-slowlog.html)，通常可以通过一些配置限制这些客户端查询。比如：
   - 减少 index.max_result_window 大小
   - 减少 search.max_buckets 集群设置的聚合桶的最大数量。
   - 使用 search.allow_expensive_queries 集群设置禁用昂贵的查询。
3. 定义过多的字段或嵌套过深的字段会导致使用大量内存的处理映射关系。在ES里直译为“映射爆炸”[mapping explosions](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/mapping.html#mapping-limit-settings)，可以使用[mapping limit settings](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/mapping-settings-limit.html)限制属性映射。
4. 分批请求数，[bulk indexing](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/docs-bulk.html)和[multi-search](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/search-multi-search.html)会导致高内存占用，如果可以的话，尽量少使用。
5. 提升节点内存
6. 避免在text类型属性上使用[fielddata](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/circuit-breaker.html#fielddata-circuit-breaker)，对于text属性，es默认禁用fielddata，尽可能用`keyword`。（[参考](https://www.elastic.co/guide/en/elasticsearch/reference/7.14/fix-common-cluster-issues.html#circuit-breaker-errors)， 如果启用了fielddata，可以使用`POST _cache/clear?fielddata=true`这个API清理cache。
7. 尽量避免 delete_by_query 删除文档，更好的方案是直接删除索引。

# 实际操作

从上面一些信息来看，我们的es节点只分配了2g的jvm内存空间。我们先看下我们四个节点服务器上的内存情况（不看swap）。

```
节点1：内存32G，22G空闲，6.3G使用，4.3G buff/cache，es节点内存占用小于10%
节点2：内存32G，22G空闲，4.7G使用，5.2G buff/cache，es节点内存占用小于10%
节点3：内存16G，283M空闲，3G+使用，12G buff/cache，es节点内存占用>20%
节点4：内存16G，2.6G空闲，约4G使用，9.7G buff/cache，es节点内存占用>20%
```

PS下java进程，主要看下es依赖的java运行环境是内嵌的还是外部的。

```bash
[root@node3 config]# ps -efl|grep java
0 S esowner   8300     1  0  80   0 - 2605170 futex_ 2021 ?       3-05:19:40 /opt/es/elasticsearch-7.6.1/jdk/bin/java -Des.networkaddress.cache.ttl=60 -Des.networkaddress.cache.negative.ttl=10 -XX:+AlwaysPreTouch -Xss1m -Djava.awt.headless=true -Dfile.encoding=UTF-8 -Djna.nosys=true -XX:-OmitStackTraceInFastThrow -Dio.netty.noUnsafe=true -Dio.netty.noKeySetOptimization=true -Dio.netty.recycler.maxCapacityPerThread=0 -Dio.netty.allocator.numDirectArenas=0 -Dlog4j.shutdownHookEnabled=false -Dlog4j2.disable.jmx=true -Djava.locale.providers=COMPAT -Xms2g -Xmx2g -XX:+UseG1GC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSInitiatingOccupancyOnly -Djava.io.tmpdir=/opt/es/elasticsearch-7.6.1/tmp -Djna.tmpdir=/opt/es/elasticsearch-7.6.1/tmp -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=data -XX:ErrorFile=logs/hs_err_pid%p.log -Xlog:gc*,gc+age=trace,safepoint:file=logs/gc.log:utctime,pid,tags:filecount=32,filesize=64m -XX:MaxDirectMemorySize=1073741824 -Des.path.home=/opt/es/elasticsearch-7.6.1 -Des.path.conf=/opt/es/elasticsearch-7.6.1/config -Des.distribution.flavor=default -Des.distribution.type=tar -Des.bundled_jdk=true -cp /opt/es/elasticsearch-7.6.1/lib/* org.elasticsearch.bootstrap.Elasticsearch -d
0 S root     22247 20981  0  80   0 - 28179 pipe_w 03:51 pts/0    00:00:00 grep --color=auto java
[root@node3 config]# 
```

从上面看出jdk在es目录下，应该是内嵌的jre环境，直接配置下/elasticsearch-7.6.1/config/jvm.options里堆大小应该就行了。

但是我们要先看一个问题，就是上面节点3的空闲内存明显太少了，而有很多内存是在buff中，有点不健康。

```bash
[root@node3 jalchu]# free -mh
              total        used        free      shared  buff/cache   available
Mem:            15G        3.5G        280M        800M         11G         10G
Swap:          3.0G        1.5M        3.0G
[root@node3 jalchu]# ps -efl|grep python
4 S root      1487     1  0  80   0 - 143485 poll_s 2021 ?        00:58:12 /usr/bin/python2 -Es /usr/sbin/tuned -l -P
4 S root     30270 29618  0  80   0 - 28179 pipe_w 05:27 pts/0    00:00:00 grep --color=auto python
[root@node3 jalchu]# 
```

node3本身只有一个ES的java 进程，1各redis哨兵进程，查看对外暴漏的TCP连接端口，看上去只有SMTP服务25、哨兵服务26379、[ES通信服务](https://zhuanlan.zhihu.com/p/505982096)（9200、9300）、ssh服务端口22。

```bash
[root@spoc-ta-job-3 jalchu]# netstat -tlun
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:43034           0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:26379           0.0.0.0:*               LISTEN     
tcp        0      0 10.241.94.34:9200       0.0.0.0:*               LISTEN     
tcp        0      0 10.241.94.34:9300       0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 10.241.94.34:123        0.0.0.0:*                          
udp        0      0 127.0.0.1:123           0.0.0.0:*                          
udp        0      0 0.0.0.0:123             0.0.0.0:*   
```

查看服务器进程持有的文件句柄数量，因为es很吃这块性能，所以查一下（PS 这个命令是拷贝项目组前辈的）。
```
[root@spoc-ta-job-3 jalchu]# lsof|awk '{print $2}'|sort|uniq -c|sort -nr|head -n 20
 123256 8300
    490 1208
    465 1487
    392 1161
    246 1225
    240 8620
    129 2077
    120 1860
    105 1903
     94 1169
     90 8322
     87 29557
     84 29543
     78 13744
     71 1
     68 1130
     58 13749
     56 1863
     53 1443
     52 7269
```

8300就是我们es进程。尝试重启es先，然后设置heap size。

kill掉es后内存占用情况：`KiB Mem : 16265624 total,  4913864 free,   353968 used, 10997792 buff/cache`

```bash
# 给下es操作用户权限
[root@spoc-ta-job-3 jalchu]# chown -R esowner:esowner /opt/es/elasticsearch-7.6.1/
# 切换es用户
[root@spoc-ta-job-3 jalchu]# su esowner
[esowner@spoc-ta-job-3 jalchu]$ /opt/es/elasticsearch-7.6.1/bin/elasticsearch -d
```

重启后再查看内存占用情况：`KiB Mem : 16265624 total,  2558356 free,  2861784 used, 10845484 buff/cache`

调整jvm.options，两台16G的我们分个3G给es，两个32G的我们分个6G给es

```
-Xms3g
-Xmx3g
```

暂时先这样。后续观察~


# 参考

https://www.elastic.co/guide/en/elasticsearch/reference/7.14/fix-common-cluster-issues.html#circuit-breaker-errors

https://blog.csdn.net/laoyang360/article/details/123059159

