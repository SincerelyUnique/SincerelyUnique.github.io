---
title: logstash采集oracle数据
date: 2022-03-09 14:34:43
tags: logstash
categories:
valine:
  placeholder: "欢迎提出意见💪"
---

# logstash采集oracle表数据

## 背景

因为项目数据量比较大，而且需要作一些metrics给大家看，所以准备先将数据存储到ES，然后通过Grafana作图。

为什么不从grafana直连oracle呢？因为Grafana连接oracle需要安装插件，并且这个插件需要付费使用[参考](https://grafana.com/grafana/plugins/grafana-oracle-datasource/?utm_source=grafana_add_ds)。所以为了节约不必要的花费，我们将数据先转存到ES（正好我们之前有搭建过es集群），然后从grafana配置es作为数据源。

现配置logstash采集oracle数据。

## 安装logstash

可以通过压缩包（tar.gz）或者RPM包等形式安装，推荐RPM包形式安装，压缩包的形式安装可以玩一玩，但是由于无法使用systemctl等一些功能，后续配置稍微比较麻烦。

### 前期准备

```bash
[root@chumingcheng logstash]# wget https://artifacts.elastic.co/downloads/logstash/logstash-7.6.1.rpm
[root@chumingcheng logstash]# rpm -ivh logstash-7.6.1.rpm
[root@chumingcheng logstash]# cd /var/log/logstash/     # logstash日志log目录
[root@chumingcheng logstash]# cd /etc/logstash/conf.d/   # logstash配置文件目录
[root@chumingcheng conf.d]# mkdir table_user.sql    # 创建采集数据的sql
[root@chumingcheng conf.d]# mkdir table_user.conf   # logstash采集配置
[root@chumingcheng conf.d]# vi table_user.sql     # 编辑sql
```

### 编辑sql脚本

```sql
select * from table_user where lastmodifiedtime > cast(:sql_last_value as date)
```

### 编辑logstash配置

```bash
[root@chumingcheng conf.d]# vi table_user.conf   # 编辑采集配置
```

先给一个简单的配置用来测试，很简单，接收控制台输入，然后将数据输出到es，并且输出到控制台，所以这里有两个输出

```
input { stdin { } }
output {
  elasticsearch {
    index => "myindextest"
    hosts => ["localhost:9200"]
    user => "elastic"
    password => "********"
  }
  stdout { codec => rubydebug }
}
```

下面是一个正式的从oracle采集并输出到es的配置

```
input{
    stdin{
    }
    jdbc{
        jdbc_connection_string => "jdbc:oracle:thin:@xx.xx.xxx.xx:1701/xxxservicename"
        jdbc_user => "xxx"
        jdbc_password => "********"
        jdbc_driver_library => "/opt/jdbc/ojdbc8-full/ojdbc8.jar"
        jdbc_driver_class => "Java::oracle.jdbc.driver.OracleDriver"
        jdbc_paging_enabled => "true"
        jdbc_page_size => "100000"
        jdbc_fetch_size => "100000"
        statement_filepath => "/etc/logstash/conf.d/table_user.sql"
        record_last_run => "true"
        use_column_value => "true"
        tracking_column => "lastmodifiedtime"
        tracking_column_type => "timestamp"
        last_run_metadata_path => "/var/log/logstash/last_run_metadata_for_table_user"
        clean_run => "false"
        schedule => "* * * * *"
        type => "table_user"
    }
}


output{
    if [type] == "table_user" {
        elasticsearch{
            hosts => ["xxx.xxx.xxx.xxx:9200"]
            user => "elastic"
            password => "**********"
            index => "table_user"
            document_id => "%{column1}_%{column2}_%{column3}"
        }
    }
}
```

测试配置并启动

```bash
[root@chumingcheng conf.d]# /usr/share/logstash/bin/logstash -f table_user.conf -t   # 测试配置
[root@chumingcheng conf.d]# /usr/share/logstash/bin/logstash -f table_user.conf   # 启动测试，看log调试
[root@chumingcheng conf.d]# systemctl start logstash   # 上一步没问题的话，执行这个命令后台启动
```

## 问题

1. 启动logstash时可能遇到/tmp路径访问的问题，执行下`mount -o remount,exec /tmp`试试
2. 也可能遇到ojdbc driver的问题，可以尝试更换下版本，比如从ojdbc10降低到ojdbc8
3. 必要时使用`chmod -777 ojdbc10`给个777权限试试


## 注意

1. 因为sql是以lastmodifiedtime为准的，所以对于老的数据没有办法删除，可以尝试通过api的方式删除es里不想要的数据
2. 第一次启动logstash后，logstash会自动去配置的表里拿所有数据，将这些数据同步到es里你新建的index下面
3. 注意给你的index创建一个index pattern，这样你就能从kibana里通过Lucene或者KQL查询你的数据了
4. 注意logstash的output里有设置一个documnet_id, 这样后续如果logstash再次抓到了一条相同document_id的数据时，就会覆盖前面一条老的数据，所以尽量用oracle表主键或聚合主键作为document_id
5. 注意用systemctl启动前进行 配置文件测试  和启动测试，这样方便看log找问题
6. 注意table_user在input和output是要关联起来的
7. 重要：理论上我应该使用更高版本的logstash去规避log4j漏洞，但是配合相应的es和kibana都要做upgrade，所以没有去做
8. 我的es和kibana都是普通用户启动的，因为有用户级别的警告，不过logstash我直接用root用户启动了，暂时没发现什么问题
9. lastmodifiedtime必须是timestamp类型或者int类型，timestamp对应oracle里Date类型，int可以对应自增主键
10. sql_last_value是固定的命名，不要试图改变它
11. 我这里是一个conf配置文件采集一个oracle表，从上面一个conf对应一个sql文件就可以看出，
12. 实际中会有很多logstash的conf配置，直接放在conf.d目录下就行，通过systemctl启动会自动加载conf.d目录下所有conf文件，不用一一指定配置文件启动。
