---
title: es合并两个属性并且terms统计count
date: 2022-09-01 08:08:36
tags:
- es
categories:
- [学习, 中间件学习, ELK]
---

# 需求

> ES里有两个fileds数据，其实性质上是一致的，都是统计cluster，但是名字不同，一个叫cluster，一个叫cluster_name，在尝试做visualize时因为terms时仅能够对其中一个filed生效，会导致另一列数据无法统计，会丢掉

# 探索

## 尝试ES官方提供的3种方法

ES官方给了三种方法，[参考地址](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-bucket-terms-aggregation.html#_multi_field_terms_aggregation)

### 方法一：Script
在dsl中写入脚本，在devtool测试脚本时报错，说没有runtime_mappings这个type，网上查询后原来我们es版本低不支持runtime_mappings，并且es不是我们team，不是想升级就能升级的。

### 方法二：copy to
这个需要在数据写入时添加copy_to属性，数据写入不是我们负责，我们是下游团队，该方法也不行。我们无权写入数据，而且是不好沟通的。

### 方法三：multi_terms
在聚合时指定多个terms，devtool执行时报错，说没有multi_terms这个type，同方法一，这个也要升级es到7.12，所以该方法也不行。

# ES团队提供的方法

在创建visualize时，设置两层聚合（即两层split series）。

- 第一个split series里terms cluster字段，并且勾选Show missing values；
- 第二个split series里terms cluster_name字段，并且勾选Show missing values

查询Inspect，可以看到DSL具体数据格式。程序里通过该DSL查询即可。这里的弊端是数据层级比较复杂。


# 参考

https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-bucket-terms-aggregation.html#_multi_field_terms_aggregation

https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-bucket-terms-aggregation.html#search-aggregations-bucket-terms-aggregation-script

https://www.elastic.co/guide/en/elasticsearch/reference/current/copy-to.html

https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-bucket-multi-terms-aggregation.html
