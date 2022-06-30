---
title: Grafana变量之间联动设置
date: 2022-06-13 14:34:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 需求说明

有时候我们想要将变量设置为级联关系，比如在做筛选的时候，先选择国家，然后加载该国家对应的省或州，最后再是省或州下面的市等。通过grafana的variable绑定变量可以将这种情况实现。

具体可以参考 [官方说明](https://grafana.com/docs/grafana/latest/datasources/elasticsearch/#query-variable)

下面是以elasticsearch为数据源的具体实现细节。

# 信息录入ES

我们可以从网上下载一份省市的关联信息表，可以直接导入es或者通过一些简单脚本写入es。

```
from elasticsearch import Elasticsearch, helpers
import pandas as pd

if __name__ == '__main__':
    sql = "select country, province, city from tb_location_mapping"
    df = pd.read_sql_query(sql, engine)
    data = df.to_dict(orient='records')
    es_host_list = '192.168.0.1,192.168.0.2,192.168.0.3'.split(',')
    es_client = Elasticsearch(hosts=es_host_list, http_auth=('es_username', 'es_password'))
    documents = [{
        "_index": 'tb_location_mapping',
        "_source": doc,
        '_id': f"{doc['country']}{doc['province']}{doc['city']}"
    } for doc in data]
    helpers.bulk(es_client, documents)
```

# 添加Grafana数据源

因为上面我们将数据写到`tb_location_mapping`这个index下面了，所以我们要在grafana里新增一个es数据源指向这个index。

# 给Dashboard添加变量

打开dashboard，点开配置，找到variable，如下新增3条变量

- country变量：`{"find": "terms", "field": "country.keyword"}`
- province变量： `{"find": "terms", "field": "province.keyword", "query": "country: $country"}`
- city变量：`{"find": "terms", "field": "city.keyword", "query": "province: $province"}`

注意下每个变量命令的name，这样通过`$`绑定才会起作用。
