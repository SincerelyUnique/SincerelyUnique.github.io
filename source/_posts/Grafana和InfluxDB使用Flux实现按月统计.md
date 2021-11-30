---
title: Grafana+InfluxDB使用Flux实现按月统计
date: 2021-11-26 02:34:43
tags: 
- grafana
- influxDB
- Flux
categories:
---

# Grafana+InfluxDB使用Flux实现按月统计

尝试按月、按年统计总数量count - statistics count monthly/yearly

# InfluxDB简介

简介：InfluxDB是GO语言编写的分布式时间序列化数据库，非常适合对数据（跟随时间变化而变化的数据）的跟踪、监控和分析。
InfluxDB的功能很强大，文档也很详细。可美中不足的是，它的单机性能并不是很理想。因为InfluxDB存储的数据量本身是非常巨大的，在执行一些时间范围比较大的sql语句，耗时会很长，甚至直接崩溃。

而开源的InfluxDB目前已经不再支持集群。若要通过搭建集群提升性能问题，可以考虑企业版。

# 方法Research

通过网上搜索，找到以下3种方法（其实还有很多，比如通过grafana其他数据源如es或者其他，这里主要查找基于influxdb实现的方法）

1. 使用InfluxQL可以尝试30d 统计周期

   参考： https://community.grafana.com/t/group-by-month-and-year/45882

2. 尝试往influxdb measurement里新增一列，存YY-MM，然后group by this column

3. InfluxQL不支持按月，但是可以使用Flux（需要InfluxDB 1.8+，我们是1.8.2，默认disable需要enable）

   https://docs.influxdata.com/influxdb/v1.8/flux/installation/

   https://docs.influxdata.com/influxdb/v1.8/flux/get-started/

   https://docs.influxdata.com/influxdb/v1.8/flux/guides/window-aggregate/#windowing-data

   https://www.influxdata.com/resources/introduction-to-flux-and-functional-data-scripting/

   As we release InfluxDB 2.0 OSS, planned for end of Oct/early Nov, we have both Flux and InfluxQL present as languages for accessing data

   https://grafana.com/docs/grafana/latest/datasources/influxdb/influxdb-flux/

   参考： https://github.com/influxdata/influxdb/issues/3991

综上所列，方法1不严谨，方法2新增冗余列也不好（虽然只是为了实现这个group的function），所以选择尝试方法3

# 尝试使用方法3

## 启用Flux
当 InfluxDB 启动时，Flux 守护进程也会启动，并且可以使用 Flux 查询数据。从 grafana 7.1 开始，grafana 直接支持 Flux。低版本可以尝试安装插件（参考：https://github.com/grafana/influxdb-flux-datasource）
```bash
[root@spoc-sj-job-3 jalchu]# whereis influxdb
[root@spoc-sj-job-3 jalchu]# cd /etc/influxdb/
[root@spoc-sj-job-3 influxdb]# vi influxdb.conf 
[root@spoc-sj-job-3 influxdb]# service influxdb restart
[root@spoc-sj-job-3 jalchu]# influx -type=flux   （ctrl+D exit）
[root@spoc-sj-job-3 jalchu]# influx -type=flux -path-prefix /api/v2/query -username <user> -password <pass> -execute 'from(bucket:"telegraf")
```

参考视频： https://www.youtube.com/watch?v=ohtUVe_B7o8   （其实也没啥，就是demo了下grafana怎么使用flux，以及使用flux可以做些什么）

## Buckets概念，或者说什么是Buckets

Flux introduces “buckets,” a new data storage concept for InfluxDB. 

A bucket is a named location where data is stored that has a retention policy. 

It’s similar to an InfluxDB v1.x “database,” but is a combination of both a database and a retention policy. 

When using multiple retention policies, each retention policy is treated as is its own bucket.

> eg： from(bucket:"<database>/<retention-policy>")

## 那么什么是retention policy呢？

A retention policy (RP) is the part of InfluxDB data structure that describes for how long InfluxDB keeps data.

InfluxDB compares your local server’s timestamp to the timestamps on your data and deletes data that are older than the RP’s DURATION. 

A single database can have several RPs and RPs are unique per database.

Note that when you create a database, InfluxDB automatically creates a retention policy named `autogen` which has infinite retention.

## 我们可以从influxdb的配置文件中开启自动创建RP，具体就像下面的
```bash
[root@spoc-sj-job-3 jalchu]# whereis influxdb
[root@spoc-sj-job-3 jalchu]# cd /etc/influxdb/
[root@spoc-sj-job-3 influxdb]# vi influxdb.conf
```
编辑内容
```
  # Automatically create a default retention policy when creating a database.
  # retention-autocreate = true
```

## 怎么手动创建RP呢？
```sql
> CREATE DATABASE "food_data"
> CREATE RETENTION POLICY "two_hours" ON "food_data" DURATION 2h REPLICATION 1 DEFAULT
> DROP RETENTION POLICY <retention_policy_name> ON <database_name>
```
Note: When we created the food_data database in step 1, InfluxDB automatically generated an RP named autogen and 
set it as the DEFAULT RP for the database. The autogen RP has an infinite retention period. 

With the query above, the RP two_hours replaces autogen as the DEFAULT RP for the food_data database.

详细的可以参考这里：https://docs.influxdata.com/influxdb/v1.7/query_language/database_management/#retention-policy-management

RP的配置文档可以参考这里：https://docs.influxdata.com/influxdb/v1.7/administration/config/#metastore-settings


## 我这边在grafana添加flux datasource，如下
```
url： http://localhost:8086/api/v2
Organization： admin （我随便写的）
Token： configured  （我没有写，自动显示的configured）
Default Bucket： your_db_name/autogen   （spoc是我们的数据库名字，autogen参考上面，是db创建后自动生成的RP）
```
但是在Save&Test的时候遇到了报错，报错信息是：message: "404 Not Found: 404 page not found\n"

打开F12查看请求，返回的response是：message: "Metric request error"，
这个时候尝试查看下grafana log
```bash
[root@spoc-sj-job-3 jalchu]# cd /var/log/grafana/
[root@spoc-sj-job-3 jalchu]# tail -f grafana.log
t=2021-11-25T02:17:07+0000 lvl=warn msg="Flux query failed" logger=tsdb.influx_flux err="404 Not Found: 404 page not found\n" query=buckets()
t=2021-11-25T02:17:07+0000 lvl=info msg="Request Completed" logger=context userId=4 orgId=1 uname=jalchu method=POST path=/api/ds/query status=400 remote_addr=10.140.203.30 time_ms=20 size=833 
referer=http://localhost:3000/datasources/edit/12/
```

参考： https://github.com/grafana/grafana/issues/26585#issuecomment-677920907

As of grafana 7.1.4, you must no longer include /api/v2 in the flux datasource anymore


## 数据源搞定后，在画图的时候出现了error，画图flux脚本是：
```flux
dataSet = from(bucket: "spoc/autogen")
  |> range(start: -30d)
  |> filter(fn: (r) => r._measurement == "bems")
  |> mean()
  |> yield(name: "_results")
```

报错是：message: "500 Internal Server Error: 

{\"error\":\"unsupported aggregate column type string\"}\n"
Possible causes: If you are trying to use an aggregator, like mean(), on a column containing string types, you will get this error. This likely means that numerical data was fed into the platform as a quoted string.

参考： https://www.influxdata.com/blog/how-to-fix-common-errors-for-beginners-in-influxdb-cloud-2-0/



## grafana可用的flux demo
```flux
import "generate"

timeRange = {start: 2021-01-01T00:00:00Z, stop: 2021-04-01T00:00:00Z}

data = generate.from(
    count: 6,
    fn: (n) => n + n,
    start: timeRange.start,
    stop: timeRange.stop
  )
  |> range(start: timeRange.start, stop: timeRange.stop)

data
  |> window(every: 1mo)
```

参考：https://docs.influxdata.com/flux/v0.x/stdlib/universe/window/#window-by-calendar-month


## 寻找测试数据并导入influxdb
```bash
[root@spoc-sj-job-3 jalchu]# curl https://s3.amazonaws.com/noaa.water-database/NOAA_data.txt -o NOAA_data.txt
[root@spoc-sj-job-3 jalchu]# influx -import -path=NOAA_data.txt -precision=s -database=NOAA_water_database
[root@spoc-sj-job-3 jalchu]# influx -precision rfc3339 -database NOAA_water_database
> show measurements;
> SELECT COUNT("water_level") FROM h2o_feet
> SELECT * FROM h2o_feet LIMIT 5
from(bucket: "NOAA_water_database/autogen")
  |> range(start: 2015-08-01T00:00:00Z, stop: 2020-10-01T00:00:00Z)
  |> filter(fn: (r) => r._measurement == "h2o_feet" and r._field == "water_level")
  |> aggregateWindow(every: 1y, fn: mean)
```

## 查询first、last record
```sql
SELECT * FROM <measurement> [WHERE <tag>=value] LIMIT 1
SELECT * FROM <measurement> [WHERE <tag>=value] ORDER BY time DESC LIMIT 1
```

## 结果

最终搞出来了， 但是显示有些差强人意，也许用ES作为数据源更好一些，因为之前搞了4台的es集群，下一步准备尝试下用ES作为数据源展示，顺带做一下KB。
先把搞出来的flux列一下：
```flux
dataSet = from(bucket: "spoc/autogen")
  |> range(start: -1y)
  |> filter(fn: (r) => r._measurement == "bems" and r._field == "title")
dataSet
  |> aggregateWindow(every: 1mo, fn: count)
```

上面的Query搞好后去Transform里选择 Add field from calculation ，Mode选择Reduce row，随便设个Alias，Enable Replace all fields。














附上一些尝试过程：

```flux
from(bucket: "db/rp")
|> range(start:-1y)
|> filter(fn:(r) => r._measurement == "my_measurement" and r._field == "column_id")
|> window(every: 1mo)
|> mean()


from(bucket: "spoc/autogen")
|> range(start:-1y)
|> filter(fn:(r) => r._measurement == "bems" and r._field == "time")
|> window(every: 1mo)
|> mean()
|> aggregateWindow(every: 1m, fn: mean)


timeRange = {start: 2021-01-01T00:00:00Z, stop: 2021-11-01T00:00:00Z}
data = from(bucket: "spoc/autogen")
  |> range(start: timeRange.start, stop: timeRange.stop)
  |> filter(fn:(r) => r._measurement == "bems" and r._field == "title" and r["tag-key-name"] == "tag-value-name")
data
  |> window(every: 1mo)


data = from(bucket: "spoc/autogen")
  |> range(start: -1y)
  |> filter(fn:(r) => r._measurement == "bems" and r["tag-key-name"] == "bemsid")
  |> map(fn:(r) => ({ r with xxxx: int(v: r["tag-key-name"]) }))
data
  |> window(every: 1mo)
  |> mean()




dataSet = from(bucket: "spoc/autogen")
  |> range(start: -1y)
  |> filter(fn: (r) => r._measurement == "bems" and r._field == "title")

dataSet
  |> window(every: 1mo)



dataSet = from(bucket: "spoc/autogen")
  |> range(start: -1y)
  |> filter(fn: (r) => r._measurement == "bems" and r._field == "title")
dataSet
  |> aggregateWindow(every: 1mo, fn: count, createEmpty: true)
  |> yield(name: "count")
  |> aggregateWindow(every: 1mo, fn: sum, createEmpty: true, column: "count")
  |> sum()
```
