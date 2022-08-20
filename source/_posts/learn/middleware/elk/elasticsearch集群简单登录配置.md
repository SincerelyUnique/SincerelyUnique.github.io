---
title: elasticsearch集群简单登录配置
date: 2021-05-14 16:46:16
tags:
- elasticsearch
categories:
- [学习, 中间件学习, ELK]
---

```raw
elasticsearch集群登录配置
 
elasticsearch默认集群配置好是没有登录认证的，直接就进去了，包括kibana也是，
这样不太安全，所以添加一个登录的过程
 
网上有很多帖子说是在elasticsearch.yml配置下面这几种参数
 http.cors.enabled: true
 http.cors.allow-origin: "*"
 http.cors.allow-headers: Authorization
 xpack.security.enabled: true
 xpack.security.transport.ssl.enabled: true
然后执行
 elasticsearch-setup-passwords interactive
然而我尝试在我们四台es集群上配置后依次启动报Connection refused错
 
 
另一种方式是尝试生成证书，然后引入证书（pkcs#12 format），这种方式可以，方法如下：
1.生成ca证书：
  执行：$ bin/elasticsearch-certutil ca
  Please enter the desired output file [elastic-stack-ca.p12]: 直接回车
  Enter password for certs: 直接回车
  执行后elasticsearch-7.6.1根目录下生成elastic-stack-ca.p12证书
2.根据第1步生成的ca证书，生成私钥：
  执行：$ bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12
  Enter password for CA (elastic-stack-ca.p12): 直接回车
  Please enter the desired output file [elastic-certificates.p12]: 直接回车
  Enter password for elastic-certificates.p12: 直接回车
  执行后elasticsearch-7.6.1根目录下生成elastic-certificates.p12私钥
3.cd进入es/config目录，创建certs，将elastic-certificates.p12拷贝进去
  $ cd elasticsearch-7.6.1/config
  $ mkdir certs
  $ cp elasticsearch-7.6.1/elastic-certificates.p12 certs/
4.同样操作，依次将elastic-certificates.p12拷贝到其他3台服务器相同路径下，
  注意es以普通用户执行，root用户会报错，要对拷贝过去的证书授予普通用户权限
5.修改elasticsearch.yml,配置下面5个参数
  $ vi config/elasticsearch.yml
  xpack.security.enabled: true
  xpack.security.transport.ssl.enabled: true
  xpack.security.transport.ssl.verification_mode: certificate
  xpack.security.transport.ssl.keystore.path: certs/elastic-certificates.p12
  xpack.security.transport.ssl.truststore.path: certs/elastic-certificates.p12
6.kill掉es各个节点进程，依次重启，$ bin/elasticsearch -d
7.集群启动成功（log正常）后，设置密码，我这边统一都设为一样的elastic521，好记
  $ bin/elasticsearch-setup-passwords interactive
  此时es登录配置就结束了，用户名默认为elastic，密码为elastic521，elastic是默认
  的es超级用户
8.下面是kibana登录，我们需要修改 $ config/kibana.yml
  elasticsearch.username: "kibana"
  elasticsearch.password: "xxx"  
  这里xxx是我们在第7步为kibana生成的密码
  重启kibana，$ bin/kibana &
9.登录kibana，使用kibana登录时会403，可以使用elastic/elastic521登录kibana
  创建一个role，再创建一个user绑定这个rule
  比如创建一个role为testrole，创建一个user为testuser
  然后kibana退出超级用户，使用testuser登录即可
  注意同时赋予kibana_admin权限
10.程序里注意添加认证,以python为例：
  from elasticsearch import Elasticsearch 
  es = Elasticsearch(hosts=['xx', 'xx', 'xx', 'xx'], http_auth=('elastic', 'elastic521'))
  res = es.get(index="xxx", id='xxx')
 
 
参考：https://www.codenong.com/cs109535035/
```
