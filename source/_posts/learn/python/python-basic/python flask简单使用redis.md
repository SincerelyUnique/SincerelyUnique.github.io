---
title: python flask简单使用redis
date: 2020-09-04 19:11:10
tags:
- python
- flask
- redis
categories:
- [学习, Python语言学习, Python]
---

1.创建app时初始化 

```python
from flask_redis_sentinel import SentinelExtension
 
 
redis_sentinel = SentinelExtension()
redis_client = redis_sentinel.master_for('mymaster')
 
 
def create_app(conf_file):
    ... ...
    redis_sentinel.init_app(app)
    ... ...
```

2.初始化app时加载config 

```python
class Config(object):
    # redis requirepass：123456
    # redis sentinel：192.168.0.3:26379,192.168.0.4:26379,192.168.0.5:26379
    REDIS_URL = 'redis+sentinel://:123456@192.168.0.3:26379,192.168.0.4:26379,192.168.0.5:26379/mymaster/0'
```

3.定义路由，引入redis client并使用

```python
from demo import redis_sentinel, redis_client, logger
from demo.api import api
 
 
@api.route('/api/redis/test', methods=['GET'])
def redis_test():
    master_info = redis_sentinel.sentinel.discover_master('mymaster')
    if master_info:
        master_ip, port = master_info
        logger.info('master ip: %s' % master_ip)
        logger.info('master port: %s' % port)
 
    slave_info = redis_sentinel.sentinel.discover_slaves('mymaster')
    if slave_info:
        slave_ip, port = slave_info
        logger.info('slave ip: %s' % slave_ip)
        logger.info('slave port: %s' % port)
 
    redis_client.set('name', 'jalen')
    name = redis_client.get('name')
    logger.info('name: %s' % name)
    return 200
```
