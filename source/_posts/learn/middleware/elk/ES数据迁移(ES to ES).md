---
title: ES数据迁移（ES to ES）
date: 2022-03-16 06:34:43
tags: es
categories:
- [学习, 中间件学习, ELK]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# ES数据迁移

> 背景：业务数据量很大，数据分布在各个dc下面，每个dc对应自己的es cluster，现需要采集每个dc下es cluster中指定数据，这里指定是指采集特定index下指定的metrics，需要通过lucene配合DSL筛选数据，现考虑两种方式。

## 方式一：使用后台job+脚本定时获取

基于我们已有的job集群环境，创建Job执行脚本（如python脚本），通过API方式采集各个es端数据源，并将这些数据存入我们的es，这种方式可以同时将数据写一份到oracle，但是因为数据量比较大，需要定期清理oracle表数据，而且如果清理时选择DML也就是delete操作的话并不能使oracle水位线降低，所以delete大表数据会影响后续表查询（扫描）性能，还可能要进行表重建，这也很麻烦，而且因为这个数据主要用来在grafana展示，写入oracle大概率只能说是备份近期数据，没有太多作用，所以可以省略该环节，并且之前提到过grafana oracle插件付费，grafana无法直接对接oracle数据源也是一个因素。

附python job脚本

```python
from elasticsearch import Elasticsearch, helpers
import pandas as pd

def es_migrate():
    client = Elasticsearch(hosts='https://xxx.xxx.com/esapi', http_auth=('username', 'password'))
    # 下面DSL可以从kibana discover Inspect打开复制过来，时间作为参数传进来，所以需要job每run一次记一次时间，然后下一次运行只需获取last run time到当前时间的
    query = {
        "query": {
            "bool": {
                "must": [
                    {
                        "query_string": {
                            "query": "metrics.appName:dajiangyou",
                            "analyze_wildcard": True,
                            "time_zone": "GMT+0"
                        }
                    }
                ],
                "filter": [
                    {
                        "range": {
                            "@timestamp": {
                                "gte": "2022-03-15T02:02:43.748Z",
                                "lte": "2022-03-15T04:02:43.748Z",
                                "format": "strict_date_optional_time"
                            }
                        }
                    }
                ],
                "should": [],
                "must_not": []
            }
        }
    }
    # client.search(index=metric['esindex'], body=dsl, request_timeout=300)
    # helpers.scan 返回一个python生成器generator
    resp = helpers.scan(
        client,
        query=query,
        index=es_index_name,
        size=10000,
    )
    data = []  # 数据写入内存记录
    for num, doc in enumerate(resp):
        print('\n', num, '', doc)
        data.append(doc)
    # 转pandas的DataFrame格式，方便使用to_sql以append方式追加至oracle表
    df = pd.DataFrame(data)
    d_type = util.set_d_type_dict(data)  # 指定类型，优化存储性能，提速
    df.to_sql('table_name', engine, if_exists='append', index=False, dtype=d_type)
    
    es_host_list = ES_HOSTS.split(',')
    my_es = Elasticsearch(hosts=es_host_list, http_auth=(ES_USERNAME, ES_PASSWORD))
    documents = [{"_index": metric['storedtable'],
                  "_source": doc,
                  '_id': f"{doc['createtime']}_{doc['env']}_{doc['dc']}"}   # 下次插入同样id时数据覆盖
                 for doc in data]
    bulk(my_es, documents)
```


https://elasticsearch-py.readthedocs.io/en/v8.1.0/
https://kb.objectrocket.com/elasticsearch/how-to-use-python-helpers-to-bulk-load-data-into-an-elasticsearch-index
https://kb.objectrocket.com/elasticsearch/elasticsearch-and-scroll-in-python-953
https://elasticsearch-py.readthedocs.io/en/7.x/helpers.html#scan


## 方式二：logstash

通过在/etc/logstash/conf.d/目录下添加logstash配置文件，自动拉去es数据源output到我们es集群。

```
input{
    elasticsearch {
        hosts => "https://xxx-xxx.xxx.com/esapi"
        index => "metrics-xxx-*"
        user => 'es_username'
        password => '************'
        query => '{ "query": { "bool": { "must": [{ "query_string": {"query": "metrics.xxx:xxxx OR metrics.xxx:xxxx"}  }]  }  } }'
        size => 500
        scroll => "5m"
        docinfo => true
        docinfo_target => "[@metadata][doc]"
        schedule => "*/5 * * * *"
    }
}


output{
    elasticsearch{
        hosts => ["xx.xx.xx.xx:9200", "xx.xx.xx.xx:9200", "xx.xx.xx.xx:9200", "xx.xx.xx.xx:9200"]
        user => "our_es_username"
        password => "*************"
        index => "my_es_index_name"
        document_id => "%{[@metadata][doc][_id]}"
    }
}
```

## 总结

当前感觉logstash采集比较方便，所以可以选择方式二，如果需要数据录入db，可以选择方式一，当然还有其他办法，比如通过apache airflow或者其他一些第三方开源项目都可以，但是第二种对于数据处理没有走程序灵活，而且通常我们从es里拿的都是聚合处理后的数据，所以两者各有优缺点
