---
title: pika Received remote Channel.Close (403) ACCESS_REFUSED
date: 2021-03-12 11:57:29
tags:
- python
- rabbitmq
categories:
- [学习, 中间件学习, Zookeeper]
---

## 起因

rabbitmq最近consumer报403 拒绝某个账号访问某个exchange，找rabbitmq team咨询，回复说最近把mq的configure权限收回了。也就是client code里不能再执行create queue/exchange的操作了。

## 异常

2021-03-12 02:30:39,268 pikaclient.py:153:INFO:Declaring exchange: xxx.xxx.exchange
2021-03-12 02:30:39,302 channel.py:1068:WARNING:Received remote Channel.Close (403): "ACCESS_REFUSED - access to exchange 'xxx.xxx.exchange' in vhost '/' refused for user 'xxxx'" on <Channel number=1 OPEN conn=<SelectConnection OPEN transport=<pika.adapters.utils.io_services_utils._AsyncSSLTransport object at 0x7fbadd054390> params=<URLParameters host=xxx.xxx.com port=5671 virtual_host=/ ssl=True>>>

## 查询

查看log，有一行declaring exchange的记录，查看code，正好有2个function的调用，就是queue_declare和exchange_declare，查看这两个方法（pika）源码，可以看到说明

```python
    def exchange_declare(self,
                         exchange,
                         exchange_type='direct',
                         passive=False,
                         durable=False,
                         auto_delete=False,
                         internal=False,
                         arguments=None):
        """This method creates an exchange if it does not already exist, and if
        the exchange exists, verifies that it is of the correct and expected
        class.
        If passive set, the server will reply with Declare-Ok if the exchange
        already exists with the same name, and raise an error if not and if the
        exchange does not already exist, the server MUST raise a channel
        exception with reply code 404 (not found).
        :param str exchange: The exchange name consists of a non-empty sequence
            of these characters: letters, digits, hyphen, underscore, period,
            or colon
        :param str exchange_type: The exchange type to use
        :param bool passive: Perform a declare or just check to see if it
            exists
        :param bool durable: Survive a reboot of RabbitMQ
        :param bool auto_delete: Remove when no more queues are bound to it
        :param bool internal: Can only be published to by other exchanges
        :param dict arguments: Custom key/value pair arguments for the exchange
        :returns: Deferred that fires on the Exchange.DeclareOk response
        :rtype: Deferred
        :raises ValueError:
        """
```

```python
    def queue_declare(self,
                      queue,
                      passive=False,
                      durable=False,
                      exclusive=False,
                      auto_delete=False,
                      arguments=None):
        """Declare queue, create if needed. This method creates or checks a
        queue. When creating a new queue the client can specify various
        properties that control the durability of the queue and its contents,
        and the level of sharing for the queue.
        Use an empty string as the queue name for the broker to auto-generate
        one
        :param str queue: The queue name; if empty string, the broker will
            create a unique queue name
        :param bool passive: Only check to see if the queue exists
        :param bool durable: Survive reboots of the broker
        :param bool exclusive: Only allow access by the current connection
        :param bool auto_delete: Delete after consumer cancels or disconnects
        :param dict arguments: Custom key/value arguments for the queue
        :returns: Deferred that fires on the Queue.DeclareOk response
        :rtype: Deferred
        :raises ValueError:
        """
```

## 解决

调用上面两个方法是，添加passive参数，置为True，即：

```python
self._channel.queue_declare(queue=queue_name, passive=True, callback=cb, durable=True)
 
...
 
self._channel.exchange_declare(exchange=exchange_name, passive=True, exchange_type=self.EXCHANGE_TYPE, durable=True, callback=cb)
```
