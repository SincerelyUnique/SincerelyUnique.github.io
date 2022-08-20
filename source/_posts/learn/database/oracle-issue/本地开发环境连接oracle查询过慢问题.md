---
title: 本地开发环境连接oracle查询过慢问题
date: 2021-11-29 14:34:43
tags: oracle
categories:
- [学习, 数据库学习, Oracle问题记录]
---

> 本地访问oracle和线上访问oracle速度差异原因排查及解决


# 测试脚本：
```python
if __name__ == '__main__':
    from sqlalchemy.engine import create_engine
    from sqlalchemy.pool import NullPool
    import pandas as pd
    import time
    import datetime

    db_url = 'xxx'
    engine = create_engine(db_url, poolclass=NullPool)

    sql = "xxx"
    start = datetime.datetime.now()
    df = pd.read_sql_query(sql, engine)
    end = datetime.datetime.now()
    print('start: %s' % start.strftime('%Y-%m-%d %H:%M:%S'))
    print('end: %s' % end.strftime('%Y-%m-%d %H:%M:%S'))
    print('End!')
```
线上执行状况测试
```bash
(venv) [root@zsj2sat302 test]# python db-test.py 
start: 2021-11-29 02:08:07
end: 2021-11-29 02:08:07
End!
```


# 故障描述
线上生产环境访问某个指定页面速度大致为2~3秒
本地开发环境访问该指定页面速度为20~30秒
其他页面同样遇到这种情况，严重影响开发效率



# 原因排查
## 猜测一： 物理机房位置原因，该PC机网段的实际网络链路效率问题
抓包测试, 线上抓包, 本地抓包.我们本地开发环境代码和线上环境代码是一样的，但是地理位置不一样，本地开发环境即个人PC在china境内，而产线机器在美国境内。所以首先猜测是因为跨太平洋远距离数据传输问题导致延迟。 其他team也感觉和产线比有一些慢。


## 猜测二： 中国GFW问题
因为GFW审查，公司内部网络走HK代理，猜测可能和代理有关，或者其他相关数据审查有关。有可能，但不好验证。


## 猜测三： Python ORM框架版本问题
将python本地虚拟环境涉及到db的第三方module版本都调整和产线环境一样后发现速度还是很慢，排除！
```
cx-Oracle            8.1.0
Flask-SQLAlchemy     2.4.4
pandas               0.25.3
SQLAlchemy           1.3.22
```


## 猜测四： OS相关环境问题
可能是windows环境或者本地oracle instant client的配置或版本问题
### 本地vmware安装centos8.4，测试centos8.4上相同python脚本执行速度
```bash
[root@localhost ~]$ cat /etc/redhat-release 
[root@localhost ~]# cd /opt/
[root@localhost opt]# whereis python
[root@localhost opt]# virtualenv -p /usr/bin/python3.6 venv
[root@localhost opt]# mkdir test
[root@localhost opt]# cd test/
[root@localhost test]# touch db-test.py
[root@localhost test]# vi db-test.py  （赋值测试code内容）
[root@localhost venv]# source bin/activate
(venv) [root@localhost venv]# pip list
(venv) [root@localhost venv]# pip install SQLAlchemy==1.3.22
(venv) [root@localhost venv]# pip install Flask-SQLAlchemy==2.4.4
(venv) [root@localhost venv]# pip install cx-Oracle==8.1.0
(venv) [root@localhost test]# pip install pandas==0.25.3
(venv) [root@localhost test]# python -V
(venv) [root@localhost test]# python db-test.py   （Cannot locate a 64-bit Oracle Client library错误，下面装oracle client）
(venv) [root@localhost opt]# mkdir oracle
(venv) [root@localhost opt]# cd oracle/
(venv) [root@localhost oracle]# wget https://download.oracle.com/otn_software/linux/instantclient/185000/instantclient-basic-linux.x64-18.5.0.0.0dbru.zip
(venv) [root@localhost oracle]# unzip instantclient-basic-linux.x64-18.5.0.0.0dbru.zip
(venv) [root@localhost oracle]# sudo yum install libaio
(venv) [root@localhost oracle]# sudo yum install libnsl
(venv) [root@localhost oracle]# vi ~/.bash_profile   （写入export LD_LIBRARY_PATH=/opt/oracle/instantclient_18_5:$LD_LIBRARY_PATH）
(venv) [root@localhost oracle]# source ~/.bash_profile 
(venv) [root@localhost test]# python db-test.py 
start: 2021-11-28 19:02:50
end: 2021-11-28 19:03:00
End!
(venv) [root@localhost test]# python db-test.py 
start: 2021-11-28 19:12:51
end: 2021-11-28 19:12:59
End!
```
### Jones从mac os帮忙测试的结果（WFH -> vpn）
```bash
start: 2021-11-29 03:14:20
end: 2021-11-29 03:14:28
End!
```
### Icon从windows虚拟的ubuntu帮忙测试的结果
```bash
start: 2021-11-29 10:23:43
end: 2021-11-29 10:23:53
End!
start: 2021-11-29 10:41:27  （换另外一种连接oracle的方式后同样慢）
end: 2021-11-29 10:41:32
```
排除！



# 解决办法
## 尝试使用iptables + NAT 配置跳板机

以三台机器ip为例

1. A: 192.168.0.1  （类比本地ip）
2. B: 192.168.0.2  （类比跳板机ip）
3. C: 192.168.0.3  （类比oracle服务器ip）

```bash
[root@localhost ~]# vi /etc/sysctl.conf
# Controls IP packet forwarding
# net.ipv4.ip_forward = 1    （设为1开启Forword）
[root@localhost ~]# /sbin/sysctl -p   （生效）
[root@localhost ~]# service iptables status
[root@localhost ~]# systemctl start iptables.service
[root@localhost ~]# service iptables status
[root@localhost ~]# iptables -L -n
[root@localhost ~]# iptables -X
[root@localhost ~]# iptables -F
[root@localhost ~]# iptables -Z

[root@localhost ~]# iptables -t nat -A PREROUTING -p tcp -m tcp --dport 11521 -j DNAT --to-destination 192.168.0.3:1701
[root@localhost ~]# iptables -t nat -A POSTROUTING -p tcp -m tcp --dport 1521 -j SNAT --to-source 192.168.0.2
[root@localhost ~]# iptables-save

[root@localhost ~]# iptables -A PREROUTING -p tcp -m tcp -d 192.168.0.2 -j DNAT --to-destination 192.168.0.3:1701
[root@localhost ~]# iptables -A POSTROUTING -p tcp -m tcp -d 192.168.0.1 -j SNAT --to-source 192.168.0.2
[root@localhost ~]# iptables-save

[root@localhost ~]# iptables -L -n
[root@localhost ~]# systemctl restart iptables.service
```

参考
1. https://cloud.tencent.com/developer/article/1115750
2. https://blog.51cto.com/quliren/2047191

## 使用socat转发数据包
```bash
[root@localhost ~]# yum install -y socat
[root@localhost ~]# #下面是监听 192.168.1.252 网卡的 15672 端口，并将请求转发至 172.17.0.15 的 15672 端口。
[root@localhost ~]#  socat  -d -d -lf /var/log/socat.log TCP4-LISTEN:15672,bind=192.168.1.252,reuseaddr,fork TCP4:172.17.0.15:15672
```

1. -d -d  前面两个连续的-d -d 代表调试信息的输出级别。
2. -lf /var/log/socat.log 指定输出信息的文件保存位置。 
3. TCP4-LISTEN:15672 在本地建立一个 TCP IPv4 协议的监听端口，也就是转发端口。这里是 15672，请根据实际情况改成你自己需要转发的端口。
4. bind 指定监听绑定的 IP 地址，不绑定的话将监听服务器上可用的全部 IP。
5. reuseaddr 绑定一个本地端口。
6. fork TCP4:172.17.0.15:15672 指的是要转发到的服务器 IP 和端口，这里是 172.17.0.15 的 15672 端口。
```python
if __name__ == '__main__':
    from sqlalchemy.engine import create_engine
    from sqlalchemy.pool import NullPool
    import pandas as pd
    import time
    import datetime

    db_url = 'oracle://username:password@192.168.1.252:15672/?service_name=xxx'
    engine = create_engine(db_url, poolclass=NullPool)

    sql = "xxx"
    start = datetime.datetime.now()
    df = pd.read_sql_query(sql, engine)
    end = datetime.datetime.now()
    print('start: %s' % start.strftime('%Y-%m-%d %H:%M:%S'))
    print('end: %s' % end.strftime('%Y-%m-%d %H:%M:%S'))
    print('End!')
```
```
start: 2021-11-29 05:50:28
end: 2021-11-29 05:50:35
End!
```
还是慢
效果不行！

参考：
1. https://blog.chaos.run/dreams/nat-vps-port-forwarding/
2. https://www.hi-linux.com/posts/61543.html

## VM环境内调试
这个是可行的，不过sftp代码开启自动同步会有一些耗时，并且每次远程连接服务器上的虚拟环境时也会有一些耗时，不过这些耗时相对较短，如果db查询实在是太慢的话可以暂时使用这种方法。否则，除非给网络team提ticket反应这个问题，让他们tracking一下链路，否则真的不好处理。

pycharm配置：
1. Tools -> Deployment -> Configuration
2. Tools -> Deployment -> Automatic Uploads(Always)
3. File -> Settings -> SSH Configurations
4. File -> Settings -> Python Interpreter -> Add -> SSH Interpreter

参考：
1. https://zhuanlan.zhihu.com/p/36843200






