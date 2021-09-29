---
title: prometheus + node_exporter
date: 2021-09-28 14:34:43
tags:
- prometheus
- node_exporter
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

## 安装node exporter采集server基础数据

```shell
[root@chumingcheng ~]# cd /opt/
[root@chumingcheng opt]# mkdir prometheus
[root@chumingcheng opt]# cd prometheus/
[root@chumingcheng prometheus]# wget github://github.com/prometheus/node_exporter/releases/download/v1.2.2/node_exporter-1.2.2.linux-amd64.tar.gz
[root@chumingcheng prometheus]# tar -zxvf node_exporter-1.2.2.linux-amd64.tar.gz
[root@chumingcheng prometheus]# cd node_exporter-1.2.2.linux-amd64
[root@chumingcheng node_exporter-1.2.2.linux-amd64]# ./node_exporter --web.listen-address=":9091" &  # 该启动可以带有一些参数，比如指定端口等，参考附录
```

启动后验证exporter是否正常运行：访问http://127.0.0.1:9091/metrics （可以看到当前server上所有性能指标）

## 安装prometheus

```shell
[root@chumingcheng ~]# cd /opt/prometheus
[root@chumingcheng prometheus]# wget https://github.com/prometheus/prometheus/releases/download/v2.29.1/prometheus-2.29.1.linux-amd64.tar.gz
[root@chumingcheng prometheus]# tar zxvf prometheus-2.29.1.linux-amd64.tar.gz 
[root@chumingcheng prometheus]# cd prometheus-2.29.1.linux-amd64/
[root@chumingcheng prometheus-2.29.1.linux-amd64]# vi prometheus.yml  # 修改yml的内容可参考下面
[root@chumingcheng prometheus-2.29.1.linux-amd64]# ./prometheus --config.file=prometheus.yml --web.listen-address=:5000 &  # 后台启动指定运行端口为5000
```

修改prometheus.yml文件指向node exporter

```
global:
  scrape_interval: 15s

scrape_configs:
- job_name: node
  static_configs:
  - targets: ['127.0.0.1:9091']
  - targets: ['x.x.x.x:9091']
```

启动后验证Prometheus是否正常运行：访问http://127.0.0.1:5000/ （进入Prometheus监控页面）

启动后验证Prometheus和exporter之间连接：输入up执行

如果Prometheus能够正常从node exporter获取数据，则会看到以下结果：

> up{instance="127.0.0.1:9091",job="prometheus"}    1

(其中"1"表示正常，反之"0"则为异常。)

或者通过http://127.0.0.1:5000/classic/targets这个link验证

## 安装grafana

```
sudo apt-get install -y adduser libfontconfig1
wget https://dl.grafana.com/enterprise/release/grafana-enterprise_8.1.5_amd64.deb
sudo dpkg -i grafana-enterprise_8.1.5_amd64.deb

sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl status grafana-server
sudo systemctl enable grafana-server.service
```

参考：

https://grafana.com/grafana/download?pg=get&plcmt=selfmanaged-box1-cta1

https://grafana.com/docs/grafana/latest/installation/debian/#2-start-the-server

## 说明

1.指定node exporter启动参数，如指定端口可以执行./node_exporter --web.listen-address=":9090"
```
--web.listen-address=":9100"  
#node_exporter监听的端口，默认是9100，若需要修改则通过此参数。

--web.telemetry-path="/metrics"  
#获取metric信息的url，默认是/metrics，若需要修改则通过此参数

--log.level="info" 
#设置日志级别

--log.format="logger:stderr"  
#设置打印日志的格式，若有自动化日志提取工具可以使用这个参数规范日志打印的格式

--collector.diskstats.ignored-devices="^(ram|loop|fd|(h|s|v|xv)d[a-z]|nvme\\d+n\\d+p)\\d+$"
#通过正则表达式忽略某些磁盘的信息收集

--collector.filesystem.ignored-mount-points="^/(dev|proc|sys|var/lib/docker/.+)($|/)"  
#通过正则表达式忽略某些文件系统挂载点的信息收集

--collector.filesystem.ignored-fs-types="^(autofs|binfmt_misc|bpf|cgroup2?|configfs|debugfs|devpts|devtmpfs|fusectl|hugetlbfs|mqueue|nsfs|overlay|proc|procfs|pstore|rpc_pipefs|securityfs|selinuxfs|squashfs|sysfs|tracefs)$"  
#通过正则表达式忽略某些文件系统类型的信息收集

--collector.netclass.ignored-devices="^$"  
#通过正则表达式忽略某些网络类的信息收集

--collector.netdev.ignored-devices="^$"  
#通过正则表达式忽略某些网络设备的信息收集

--collector.netstat.fields="^$"
#通过正则表达式配置需要获取的网络状态信息
 
--collector.vmstat.fields="^(oom_kill|pgpg|pswp|pg.*fault).*" 
#通过正则表达式配置vmstat返回信息中需要收集的选项
```

## 问题记录

1.wget时报Unsupported scheme ‘github’

将github替换为https再尝试试试，如果实在不行的话本地下载后sftp到服务器上也可以的，不得不吐槽国内的github访问速度

2.我这边实际操作2台server，腾讯云部署node exporter，aliyun部署Prometheus，也就是用aliyun来监控腾讯云，因为腾讯云最近经常被ddos，但是发现两边都部署好后，访问http://x.x.x.x:5000/classic/targets报Get "http://x.x.x.x:9091/metrics": context deadline exceeded

一开始参考：https://stackoverflow.com/questions/49817558/context-deadline-exceeded-prometheus，配下timeout，仍然不行。

aliyun服务器尝试curl http://110.42.128.97:9091/metrics也报timeout

后来想到腾讯云有配置安全组，拦截了除我本机所属网段外的所有其他网段（包括拦截了aliyun 47开头的网段）

在腾讯云安全组将9091端口开放给aliyun当前ip后就好啦

## 参考

https://github.com/prometheus/node_exporter

https://prometheus.io/docs/guides/node-exporter/

https://blog.csdn.net/weixin_44723434/article/details/89237202

https://yunlzheng.gitbook.io/prometheus-book/parti-prometheus-ji-chu/quickstart/prometheus-quick-start/use-node-exporter

https://stackoverflow.com/questions/53295711/multiple-targets-on-prometheus (bind multi exporter)

https://grafana.com/grafana/download?pg=get&plcmt=selfmanaged-box1-cta1 （install grafana）

https://grafana.com/docs/grafana/latest/installation/debian/#2-start-the-server (start grafana)

https://grafana.com/docs/grafana/latest/getting-started/getting-started/ (grafana default username/pass)


