---
title: python redis客户端简单使用
date: 2020-09-04 18:56:54
tags:
- python
- redis
categories:
---

哨兵配置参考：https://blog.csdn.net/baidu_30809315/article/details/108409780 

```raw
    pip install redis
```

```python
def redis_sentinel_client():
    from redis.sentinel import Sentinel
    conf = {
        'sentinel': [('192.168.0.3', 26379), ('192.168.0.4', 26379), ('192.168.0.5', 26379)],
        'master_group_name': 'mymaster',
        'connection_conf': {
            'socket_timeout': 0.5,
            'retry_on_timeout': True,
            'socket_keepalive': True,
            'max_connections': 10,
            'db': 0,
            'encoding': 'utf8',
            'decode_responses': True,
        }
    }
    sentinel = Sentinel(conf['sentinel'], **conf['connection_conf'])
    ip, port = sentinel.discover_master(conf['master_group_name'])
    logger.info('master ip: %s' % ip)
    logger.info('master port: %s' % port)
    sentinel_client = sentinel.master_for(conf['master_group_name'], password='123456')
    sentinel_client.set('name', 'jalen')
    sentinel_client.get('name')
```
