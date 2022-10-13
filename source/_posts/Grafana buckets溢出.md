---
title: Grafana buckets溢出
date: 2022-10-13 15:07:49
tags:
- grafana
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 原因

Grafana作图时折线图中点的数据量太多，聚合粒度太细，导致需要从ES拿去的数据过多

# 提给Grafana的建议

建议Grafana可以使用scroll查询，能多次取数据整合

# 自身优化

方法一： 作图时尽量不要指定具体interval变量，如果你指定的interval粒度很小，比如每几分几秒就要展示一个点，当时间范围选择很大时，很容易出现buckets溢出

方法二： 作图时，比如Terms里，指定好Min Doc Count为1，不要为0，这样会减少不必要的buckets获取

方法三： 在kibana页面，进入Dev Tools，在tools里执行put请求，设置可查询的buckets的最大数量，因为默认是10000，这里可以调整为100000或更多，比如
```
PUT _cluster/settings
{
  "transient": {
    "search.max_buckets": 100000
  }
}
```

方法四（参考）： 看github有个讨论里说设置Grafana Query options里Min interval和Max data points也会提高一定性能


# 参考：

https://discuss.elastic.co/t/increasing-max-buckets-for-specific-visualizations/187390

https://www.elastic.co/guide/en/elasticsearch/reference/master/search-aggregations-bucket.html

https://www.elastic.co/guide/en/elasticsearch/reference/master/search-settings.html#search-settings-max-buckets

