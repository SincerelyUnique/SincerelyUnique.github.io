---
title: windows简单安装使用rabbitmq
date: 2020-08-17 12:09:42
tags:
- rabbitmq
categories:
---

```
rabbitmq简单了解
参考：https://www.rabbitmq.com/install-windows-manual.html
 
1. 安装
（1）简介
安装rabbitmq需要先安装erlang语言运行环境，因为rabbitmq是使用erlang语言编写的，如果
不安装erlang，在启动rabbitmq时会报ERLANG_HOME not set correctly. （windows环境下）。
erlang语言是一种通用的并发程序设计语言，也是运行于虚拟机之上的一种解释型语言，最早
由爱立信实验室研发，后开源，涵盖函数式、并行及分布式。
 
（2）安装erlang环境
erlang官网：https://www.erlang.org/downloads
erlang下载：http://erlang.org/download/otp_win64_23.0.exe  （88M大小，磁盘空间300M+）
ERLANG_HOME：D:\erlang\erl-23.0 （rabbitmq batch文件会执行%ERLANG_HOME%\bin\erl.exe.）
 
（3）下载rabbitmq
我下载的是zip包，名字是rabbitmq-server-windows-3.8.6.zip。
下载地址：https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.6/rabbitmq-server-windows-3.8.6.zip
 
（4）同步erlang cookie -- 跳过此步骤
erlang cookie是一个在rabbitmq节点和cli工具之间共享的密钥，这个值存储在erlang cookie文件中。
在使用rabbitmqctl.bat启动时会使用到这个cookie文件，并且集群中所有节点必须使用相同的cookie值。
 
（5）运行方式
rabbitmq执行脚本在rabbitmq_server-3.8.6\sbin目录下。
rabbitmq可以以application形式运行，也可以以service形式运行。
rabbitmq-server.bat 以application形式运行；
rabbitmq-service.bat 以service形式运行；
rabbitmqctl.bat 管理中间件
 
（6）节点数据存储
存储目录：C:\Documents and Settings\%USERNAME%\Application Data
          C:\Users\%USERNAME%\AppData\Roaming 
想确定具体是上面哪个目录可以在cmd下输入：echo %APPDATA%
 
（7）以app方式启动
cmd执行：rabbitmq-server.bat    （想要后台运行，可以加-detached参数）
报错：BOOT FAILED ERROR: epmd error for host JALCHU-6JT6N: address (cannot connect to host/port)
参考：https://stackoverflow.com/questions/38343656/epmd-error-for-host-myhost-address-cannot-connect-to-host-port-on-windows-10
解决办法：添加环境变量，RABBITMQ_NODENAME=rabbit@localhost，添加后重新执行rabbitmq-server.bat即可。
查看状态：rabbitmqctl.bat status
关闭：rabbitmqctl.bat stop
 
（8）以windows service方式启动
cmd执行：rabbitmq-service.bat install
报错：Unable to register RabbitMQ service with service manager.Error: Access is denied.
解决办法：一般我们打开cmd默认都是普通用户，我们可以在左下角搜索cmd然后以管理员身份打开cmd窗口即可。
查看：cmd执行services.msc可以看到RabbitMQ service，右键启动。
一些基本命令
rabbitmq-service help    - Display this help
rabbitmq-service install - Install the RabbitMQ service
rabbitmq-service remove  - Remove the RabbitMQ service
rabbitmq-service start   - Start the RabbitMQ service
rabbitmq-service stop    - Stop the RabbitMQ service
rabbitmq-service disable - Disable the RabbitMQ service
rabbitmq-service enable  - Enable the RabbitMQ service
 
（9）启用管理插件
启用插件，cmd执行：rabbitmq-plugins.bat enable rabbitmq_management
禁用插件，cmd执行：rabbitmq-plugins.bat disable rabbitmq_management
在service启动前提下启用插件，浏览器输入http://127.0.0.1:15672/，
初始登录默认用户名和密码都是guest
进入后可以看到当前节点的一些配置，如Memory，Disk space
```

```python
import pika
 
 
def emit_log_topic():
    connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
    channel = connection.channel()
    channel.exchange_declare(exchange='topic_logs', exchange_type='topic')
    routing_key = 'test'  # bind message key
    message = 'Hello World123!'  # bind message
    channel.basic_publish(exchange='topic_logs', routing_key=routing_key, body=message)
    print(" [Producer] sent %r:%r" % (routing_key, message))
    connection.close()
 
 
if __name__ == '__main__':
    print('start pika producer...')
    emit_log_topic()
```

```python
import pika
 
 
def receive_logs_topic():
    credentials = pika.credentials.PlainCredentials(
        username='guest',
        password='guest'
    )
    connection_params = pika.ConnectionParameters(
        host='localhost',
        port=5672,
        connection_attempts=5,
        retry_delay=5,
        credentials=credentials
    )
    connection = pika.BlockingConnection(connection_params)
    channel = connection.channel()
    channel.exchange_declare(exchange='topic_logs', exchange_type='topic')
    result = channel.queue_declare('', exclusive=True)
    queue_name = result.method.queue
 
    for binding_key in ['test']:  # receive message key
        channel.queue_bind(
            exchange='topic_logs', queue=queue_name, routing_key=binding_key)
    print('[*] Waiting for logs. To exit press CTRL+C')
 
    channel.basic_consume(queue=queue_name, on_message_callback=callback, auto_ack=True)
    channel.start_consuming()
 
 
def callback(channel, method, properties, body):
    print(" [Consumer] receive %r" % body)
 
 
if __name__ == '__main__':
    print('start pika consumer...')
    receive_logs_topic()
```
