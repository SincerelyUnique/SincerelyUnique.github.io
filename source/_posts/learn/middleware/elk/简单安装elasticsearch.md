---
title: 简单安装elasticsearch
date: 2021-02-05 15:24:19
tags:
- es
categories:
- [学习, 中间件学习, ELK]
---

```raw
elastic search 安装记录
1.es需要普通用户来操作（如启动），包括kibana也是，需要创建一个普通用户，大致命令如下
[root@localhost ~]# groupadd elasticsearch   #添加用户组
[root@localhost ~]# useradd esowner          #添加用户
[root@localhost ~]# passwd esowner           #设置该用户密码
[root@localhost ~]# mkdir ‐p /usr/local/es     #用户存放es解压后的包
[root@localhost ~]# usermod ‐G elasticsearch esowner    #添加到用户组
[root@localhost ~]# wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.1-linux-x86_64.tar.gz   #下载
[root@localhost ~]# tar -zxvf elasticsearch-7.6.1-linux-x86_64.tar.gz     #加压
[root@localhost ~]# chown ‐R esowner /usr/local/es/elasticsearch‐7.6.1    #授权
[root@localhost ~]# visudo   #在root ALL=(ALL) ALL 一行下面添加esowner ALL=(ALL) ALL
[root@localhost ~]# su esowner  #切换普通用户
[root@localhost ~]# whoami  #检测
 
 
[root@localhost ~]# cd /usr/local/es/elasticsearch‐7.6.1/config
[root@localhost ~]# mkdir ‐p /usr/local/es/elasticsearch‐7.6.1/log    #存日志
[root@localhost ~]# mkdir ‐p /usr/local/es/elasticsearch‐7.6.1/data   #存kibana数据
[root@localhost ~]# mkdir ‐p /usr/local/es/elasticsearch‐7.6.1/tmp    #jvm临时数据
 
 
[root@localhost ~]# vi elasticsearch.yml   #打开下面配置项，默认是注释掉的，服务器IP写当前ip即可
	cluster.name: cluster.name: my-application   
	node.name: node-1
	path.data: /usr/local/es/elasticsearch‐7.6.1/data
	path.logs: /usr/local/es/elasticsearch‐7.6.1/log
	network.host: 0.0.0.0
	http.port: 9200
	discovery.seed_hosts: ["服务器IP"]
	cluster.initial_master_nodes: ["服务器IP"]
	bootstrap.system_call_filter: false
	bootstrap.memory_lock: false
	http.cors.enabled: true
	http.cors.allow‐origin: "*"
[root@localhost ~]# vi jvm.options
        -Xms2g  #默认1g则调为2g
	-Xmx2g  #默认1g则调为2g
 
        8-13:-XX:+UseG1GC   #默认gc为8-13:-XX:+UseConcMarkSweepGC，改为 8-13:-XX:+UseG1GC
 
	-Djava.io.tmpdir=/usr/local/es/elasticsearch-7.6.1/tmp    #避免普通用户访问/tmp出现权限问题
	-Djna.tmpdir=/usr/local/es/elasticsearch-7.6.1/tmp        #避免普通用户访问/tmp出现权限问题
[root@localhost ~]# vi bin/elasticsearch-env   # 是用自带jdk这里可以改下
	# now set the path to java
	#if [ ! -z "$JAVA_HOME" ]; then
	#  JAVA="$JAVA_HOME/bin/java"
	#  JAVA_TYPE="JAVA_HOME"
	#else
	if [ "$(uname -s)" = "Darwin" ]; then
	  # macOS has a different structure
	  JAVA="$ES_HOME/jdk.app/Contents/Home/bin/java"
	else
	  JAVA="$ES_HOME/jdk/bin/java"
	fi
	JAVA_TYPE="bundled jdk"
	#fi
[root@localhost ~]# chown ‐R esowner:esowner /usr/local/es/elasticsearch-7.6.1  #再次授权，避免启动时权限问题
[root@localhost ~]# /usr/local/es/elasticsearch-7.6.1/bin/elasticsearch  #启动es
 
 
[root@localhost ~]# wget https://artifacts.elastic.co/downloads/kibana/kibana-7.6.1-linux-x86_64.tar.gz
[root@localhost ~]# tar -zxvf kibana-7.6.1-linux-x86_64.tar.gz
[root@localhost ~]# vi kibana.yml
	server.port: 5601
	server.host: "服务器IP"   # 0.0.0.0
	elasticsearch.hosts: ["http://IP:9200"]   #这里是elasticsearch的访问地址
[root@localhost ~]# /usr/local/es/kibana-7.6.1-linux-x86_64/bin/kibana   #启动kibana
 
 
参考图灵学院白起老师的文档（有几项针对普通用户的限制，可以拓展一下）：
1.max file descriptors [4096] for elasticsearch process likely too low, increase to at least [65536]
[root@localhost ~]# sudo vi /etc/security/limits.conf  #添加下面内容，注意带*号
	* soft nofile 65536
	* hard nofile 65536
2.max number of threads [1024] for user [es] likely too low, increase to at least [4096]
[root@localhost ~]# sudo vi /etc/security/limits.d/20‐nproc.conf    #centos6是90‐nproc.conf
	* soft nproc 1024#修改为
	* soft nproc 4096
3.max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]
[root@localhost ~]# sudo vi /etc/sysctl.conf
        vm.max_map_count=655360    #结尾添加一行
 
 
 
问题：
1.windows安装es7.10.2，python安装elasticsearch7.10.1后，执行example usage（https://elasticsearch-py.readthedocs.io/en/v7.10.1/）后
  再次创建index会报超时问题，即便修改request_timeout的值为5min也不行，就是无法创建index，但是可以查询，比如输入
  http://localhost:9200/test-index/_doc/1
 
2.OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
参考：https://blog.csdn.net/xiaoxiong_web/article/details/105597150
解决：修改config/jvm.options，将gc配置由8-13:-XX:+UseConcMarkSweepGC改为8-13:-XX:+UseG1GC
 
3.es7依赖jdk11，使用自带jdk
参考：https://blog.csdn.net/xiaoxiong_web/article/details/105597150
解决：修改bin/elasticsearch-env文件，找到文件钟JAVA="$JAVA_HOME/bin/java"，修改一下指向或只保留下面else里语句
 
4.java.lang.IllegalArgumentException: unknown setting [http.cors.allow‐origin] did you mean [http.cors.allow-origin]?
解决：这个很奇怪，可能是复制过去有些问题吧，将do you mean后面的复制过去就好了
 
5.java.lang.UnsatisfiedLinkError: /tmp/elasticsearch-14795050142464540793/jna-3506402/jna8067215868334079374.tmp: /tmp/elasticsearch-14795050142464540793/jna-3506402/jna8067215868334079374.tmp: failed to map segment from shared object: Operation not permitted
参考：https://blog.csdn.net/u013066244/article/details/78698340
修改：创建/usr/local/es/elasticsearch-7.6.1/tmp，编辑config/jvm.options
      -Djava.io.tmpdir=/usr/local/es/elasticsearch-7.6.1/tmp
      -Djna.tmpdir=/usr/local/es/elasticsearch-7.6.1/tmp
 
6.java.nio.file.AccessDeniedException: /usr/local/es/elasticsearch-7.6.1/config/elasticsearch.keystore
参考：https://blog.csdn.net/chengyuqiang/article/details/89845051
执行：sudo chown -R esowner:esowner elasticsearch.keystore
      或者直接从es根目录重新授权
```
