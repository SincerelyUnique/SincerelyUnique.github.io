---
title: pika加载证书连接rabbitmq（5671端口）
date: 2020-11-02 15:50:13
tags:
- python
- pika
- rabbitmq
categories:
- [学习, 中间件学习, Zookeeper]
---

```
def start_consumer():
    import jks  # 安装pyjks
    from OpenSSL import crypto  # 安装openssl，module好像叫pyopenssl
    from urllib import quote, urlencode  # 密码中特殊字符做quote， url中特殊字符做urlencode
 
    username = 'guest'
    password = quote('guest@password')
    p12_path = r'certificate\keystore\client\client_key.p12'
    keystore_path = r'certificate\keystore\rabbitStore'
 
    keystore = jks.KeyStore.load(keystore_path, 'rabbit')
    cert = keystore.certs['rabbit-server'].cert
    key_x_509 = crypto.load_certificate(crypto.FILETYPE_ASN1, cert)
    key_asn = crypto.dump_certificate(crypto.FILETYPE_ASN1, key_x_509)
    key_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, key_x_509)
    key_pem_file = open('key.pem', 'wb')
    key_pem_file.write(key_pem)
    key_pem_file.close()
 
    # 加载p12，p12里有1个私钥、3个证书（其中有2个ca证书）
    p12 = crypto.load_pkcs12(open(p12_path, 'rb').read(), 'rabbit')
    cert_asn = crypto.dump_certificate(crypto.FILETYPE_ASN1, p12.get_certificate())
    cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, p12.get_certificate())
    cert_pem_file = open('cert.pem', 'wb')
    cert_pem_file.write(cert_pem)
    cert_pem_file.close()
 
    cacert_asn_1 = crypto.dump_certificate(crypto.FILETYPE_ASN1, p12.get_ca_certificates()[0])
    cacert_asn_2 = crypto.dump_certificate(crypto.FILETYPE_ASN1, p12.get_ca_certificates()[1])
    cacert_pem_1 = crypto.dump_certificate(crypto.FILETYPE_PEM, p12.get_ca_certificates()[0])
    cacert_pem_2 = crypto.dump_certificate(crypto.FILETYPE_PEM, p12.get_ca_certificates()[1])
    cacert_pem_file = open('cacert.pem', 'wb')
    cacert_pem_file.write(cacert_pem_1)
    cacert_pem_file.write(cacert_pem_2)
    cacert_pem_file.close()
 
    pk_asn = crypto.dump_privatekey(crypto.FILETYPE_ASN1, p12.get_privatekey())
    pk_pem = crypto.dump_privatekey(crypto.FILETYPE_PEM, p12.get_privatekey())
    pk_pem_file = open('pk.pem', 'wb')
    pk_pem_file.write(pk_pem)
    pk_pem_file.close()
 
    # ca_certs/cert_reqs/certfile/keyfile/ssl_version/password
    ssl_options = urlencode({'ssl_options': {'ca_certs': 'cacert.pem', 'certfile': 'cert.pem', 'keyfile': 'pk.pem'}})
    amqp_url = 'amqps://{0}:{1}@xx.xx.xx.xx:5671?heartbeat=30&{2}'.format(username, password, ssl_options)
    
    # 下面连接的类参考（异步）：https://blog.csdn.net/baidu_30809315/article/details/108716198
    consumer = ReconnectingMQConsumer(amqp_url)
    consumer.run()
```

```
遇到的问题：
 
pip install pyjks
 
安装pyjks时其中一个依赖-twofish windows安装报错
原因：twofish包含c99 file
c99定义：a past version of the C programming language standard
linux似乎需要特定gcc才能够编译c99 file
windows需要安装MinGW
https://github.com/kurtbrose/pyjks/issues/32
 
 
python没有像java专门提供的密钥库KeyStore，pyjks是类似java keystore的一个module，
但是python有ssl
https://stackoverflow.com/questions/33790315/python-equivalent-of-javas-keystore
 
 
how to load a pkcs12 keystore using python
https://stackoverflow.com/questions/60837051/how-to-load-a-pkcs12-keystore-using-python
# p12 = crypto.load_pkcs12(open(client_key_path, 'rb').read(), 'rabbit')
# ctx = OpenSSL.SSL.Context(OpenSSL.SSL.TLSv1_2_METHOD)
# ctx.use_privatekey(p12.get_privatekey())
# ctx.use_certificate(p12.get_certificate())
# ctx.add_client_ca(p12.get_ca_certificates())
使用pika.BlockingConnection在组装ConnectionParameters时可以尝试上面方式，但是我没有搞定，
因为BlockingConnection是pika同步连接，我们已经弃用了，目前使用的是SelectConnection异步连接
方式连mq server，同时使用URLParameters配置连接参数，异步连接在docs上说性能好一些。
 
 
pyjks使用
https://pyjks.readthedocs.io/en/latest/examples.html
https://pypi.org/project/pyjks/
```
