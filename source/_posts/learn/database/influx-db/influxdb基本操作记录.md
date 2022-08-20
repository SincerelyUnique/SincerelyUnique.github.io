---
title: influxdb基本操作记录
date: 2021-05-24 16:50:12
tags:
- influxdb
categories:
- [学习, 数据库学习, InfluxDB]
---

```bash
1.概念
InfluxDB里存储的数据被称为时间序列数据,InfluxDB存储方式跟传统关系型数据库不同的是：
传统关系型数据库通过数据库+表+字段组织数据，InfluxDB通过指标、标签、字段组织数据，
时间戳是默认的索引列，标签跟字段其实就相当于关系型数据库中的字段，只不过标签会被索
引，而字段不会。
（1）database：数据库
（2）measurement：度量，相当于table；例如：cpu
（3）tags：标签，相当于field，会被索引；例如：host
（4）field：字段，不会被索引，例如：cpu_load
 
 
2.安装
sudo yum install -y influxdb
 
 
3.启动
sudo systemctl start influxdb  #启动
sudo systemctl enable influxdb  #配置开机启动
 
 
4.防火墙
firewall-cmd --state
firewall-cmd --add-port=8086/tcp --permanent  #开放端口
firewall-cmd --reload   #重载防火墙配置
 
 
5.操作
（1）进入influxdb
语法：$ influx
（2）插入
语法：INSERT cpu,host=192.168.1.1 load=0.1,usage=0.2
Insert的时候如果没有带时间戳，InfluxDB会自动添加本地的当前时间作为它的时间戳。
（3）查询
语法：SELECT * FROM "cpu"
语法：SELECT "host","load","usage" FROM "cpu"
语法：SELECT "host","load","usage" FROM "cpu" WHERE "host" = '192.168.1.1'
语法：SELECT "host","load","usage" FROM "cpu" WHERE "usage" > 0.1
（4）创建数据库
语法：CREATE DATABASE "db_name"
（5）显示所有数据库
语法：SHOW DATABASES
（6）删除数据库
语法：DROP DATABASE "db_name"
（7）使用数据库
语法：USE mydb
（8）显示该数据库中的表
语法：SHOW MEASUREMENTS
（9）删除表
语法：DROP MEASUREMENT "t_name"
 
 
 
参考：
https://ken.io/note/centos7-influxdb-install-introduction （CentOS 7下InfluxDB部署与使用入门）
```

```python
if __name__ == '__main__':
    # https://github.com/influxdata/influxdb-python
    # pip install influxdb
    from influxdb import InfluxDBClient
    from datetime import datetime
 
    client = InfluxDBClient(host='xx.xx.xx.xx', port=8086, database='xxx')
    json_body = [{
        "measurement": "xxx",
        "tags": {
            "host": "server01",
            "region": "us-west"
        },
        "time": datetime.utcnow(),
        "fields": {
            "value": 0.64,
            'name': 'xxx',
            'age': 'xxx',
        }
    }]
    client.write_points(json_body)
    result = client.query('select * from xxx;')
    logger.info("Result: {0}".format(result))
```

```python
def refresh_influx():
    from influxdb import InfluxDBClient

    from project import engine, logger
    import pandas as pd

    sql = "select * from TABLE_TEST"
    df = pd.read_sql_query(sql, engine)
    df = df[['uuid', 'text', 'createtime']]
    df['uuid'] = df['code'].fillna('None')  # influxQL查询时候可以写 !='None', 好像没有is not null的判断
    df['text'] = df['text'].fillna('None')
    df = df.fillna('')

    client = InfluxDBClient(host='192.168.0.2', port=8086, database='test')

    logger.info('Delete old data.')
    for uid in df['uuid'].tolist():
        params = {"db": "test", "q": "delete from mm_test where code='%s'" % uid}
        client.request('query', 'get', params=params)

    logger.info('Insert new data.')
    df_insert = df.copy()
    df_insert["measurement"] = "test"
    df_insert["fields"] = df_insert.apply(lambda x: {
        'uuid': x['uuid'],
        'text': x['text'],
        'createtime': x['createtime']
    }, axis=1)
    df_insert["tags"] = df_insert.apply(lambda x: {
        'uuid': x['uuid'],
        'text': x['text']
    }, axis=1)  # 只有设置为tag才能够作为influxQL查询条件
    df_insert["time"] = df_insert['createtime']  # 指定时间序列
    df_insert = df_insert[['measurement', 'fields', 'tags', 'time']]
    client.write_points(df_insert.to_dict(orient='records'))
    logger.info('refresh success')
```
