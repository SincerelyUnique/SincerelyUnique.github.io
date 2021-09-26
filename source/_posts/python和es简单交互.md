---
title: python和es简单交互
date: 2021-02-08 17:16:29
tags:
- python
- elasticsearch
categories:
---

```python
from elasticsearch import Elasticsearch
 
 
def op_es(action, info):
    """
    这里大致是一个增删改的操作，注意这里的es的id是info['id']，可以类比关系型db里的主键，比如是uuid，
    如果是将关系型db的数据写入es，完全可以使用db里的主键充当es的id
    :param action: type str，add/delete/edit，类似于我们经常操作关系型数据库，执行增/删/改操作
    :param info: type dict，类似于db一条记录，这条记录在es里大致叫做doc，info字典里的每个key叫做field
    :return: None
    """
    es = Elasticsearch(hosts=['x.x.x.x'])  # 连接es，端口默认9200
    es_index = 'xx-index'  # 即将操作的es index
    if action == 'delete':
        es.delete(index=es_index, id=info['id'])  # 删除该索引中这个id对应的doc
        return None
 
    body = {'id': info['id'], 'name': info['name'], 'detail': info['detail']}
    if action == 'add':
        es.create(index=es_index, id=info['id'], body=body)  # 在该索引下创建一个新的doc
    elif action == 'edit':
        es.update(index=es_index, id=info['id'], body={"doc": body})  # 更新已有的某个doc
 
 
def query_es(query_string):
    es = Elasticsearch(hosts=['x.x.x.x'])
    es_index = 'xx-index'
 
    # filter: 询结果里能够看到score都是0，速度要快些
    query = es.search(index=es_index, body={"query": {"bool": {
        "filter": [
            {
                "multi_match": {
                    "query": query_string,
                    'fields': ['name', 'detail']
                }
            }
        ],
    }}, 'size': 20})
 
    # query: 查询结果里能够看到score计算值，速度要慢些，但是具体还是看数据量以及具体查询DSL的语法
    query = es.search(index=es_index, body={"query": {
        'multi_match': {
            'query': query_string,
            'fields': ['name', 'detail']
        }
    }, 'size': 20})
    return {'data': [q['_source'] for q in query['hits']['hits']]}
```

```raw
一些说明和可能需要了解的东西
 
有时间的话可以看下官方文档
https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html
如果时间不多，也可以打开这个文档，看下每个目录下面的overview简单地了解下
 
 
DSL查询：es查询方式之一，结构化查询，使用json请求体，直观简易，使用率高。
（新版本有个EQL，处于beta阶段，代码不如DSL成熟，但是也是提供的，使用不提供任何担保）
 
 
Elasticsearch DSL中有Query与Filter两种
（1）Query方式查询，会在ES中索引的数据中存储一个_score分值，分值越高
     就代表越匹配。注意搜索的分值计算还是很复杂的（根据lucene的评分机制(TF/IDF)来
     进行评分），因此也需要一定的时间开销
（2）Filter过滤器方式查询，它的查询不会计算相关性分值，也不会对结果
     进行排序, 因此效率会高一点，查询的结果可以被缓存
参考：https://www.elastic.co/guide/en/elasticsearch/reference/current/query-filter-context.html
 
 
es查询
（1）term：term查询不会对字段进行分词查询且精确匹配，查询字段映射类型属于为keyword
     参考：https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-term-query.html
（2）match：根据备注信息模糊查询 match，match会根据该字段的分词器，进行分词查询
     参考：https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html
（3）multi_match：多字段模糊匹配查询
     参考：https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-multi-match-query.html
（4）match_phrase：同时包含、存在可调节因子slop
     参考：https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query-phrase.html
（5）best_fields：希望完全匹配的文档占的评分比较高
（6）范围查询：比如事件段切割gte、lte、format等
（8）布尔查询：https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html
     must: 文档必须完全匹配条件
     should: should下面会带一个以上的条件，至少满足一个条件，这个文档就符合should
     must_not: 文档必须不匹配条件
 
 
es的scroll查询（或游标查询）类似一种深度分页机制，默认10000，python es插件默认10（需在DSL里不从size）
可以针对大批量的文档进行查询，而又不用付出深度分页那种代价。即取某个时间点的快照数据。
启用游标查询：查询的时候设置参数 scroll 的值为我们期望的游标查询的过期时间，
游标查询的过期时间会在每次做查询的时候刷新，所以这个时间只需要足够处理当前批的结果就可以了。
保持这个游标查询窗口需要消耗资源，数据处理完毕后尽量早些释放。
scroll=1m， 保持游标查询窗口一分钟。
注意：
注意游标查询每次返回一个新字段 _scroll_id。每次我们做下一次游标查询， 
我们必须把前一次查询返回的字段 _scroll_id 传递进去。 当没有更多的结果返回的时候，
我们就处理完所有匹配的文档了。
 
 
tf-idf
tf（term frequency，tf）：指的是某一个给定的词语在该文件中出现的频率
idf（inverse document frequency，idf）：逆向文件频率是一个词语普遍重要性的度量
计算公式参考：https://zh.wikipedia.org/wiki/Tf-idf
```
