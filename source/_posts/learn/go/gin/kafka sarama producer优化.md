---
title: kafka sarama producer优化
date: 2023-09-06 09:28:47
tags:
- go
- gin
categories:
- [学习, Go语言学习, Gin框架]
---

## background

使用goflow(https://github.com/cloudflare/goflow) 获取各个设备udp package， 基于snmp协议(netflow), goflow解析json后发送到kafka某个topic,下游消费

压测:
- cpu (producer/consumer)
- memory (producer/consumer)
- kafka lag ()
- pps (netstat -su)

压测峰值(iptraf-ng)：
- 2000 pps
- 200M/s

topic
- PartitionCount： 64

producer：
- vm: cpu = 8 cores
- vm: mem = 32 G
- go app: channel = 8
- go app: worker = 300

consumer：
- vm: cpu = 8 cores
- vm: mem = 32 G
- java thread = 64 （-> map one partition）

use samplicate in docker to distribute UDP package

ref to: https://github.com/IBM/sarama/issues/2649

sarama version: github.com/Shopify/sarama v1.29.1

## solution

```go
	kafkaConfig.Producer.RequiredAcks = 0
	kafkaConfig.Producer.Retry.Max = 0
	kafkaConfig.Producer.Compression = sarama.CompressionLZ4   //core
	kafkaConfig.Producer.Flush.Messages = 5044288
	kafkaConfig.Producer.Flush.Frequency = time.Duration(1000) * time.Millisecond
	kafkaConfig.Producer.Flush.Bytes = 33554432
```
