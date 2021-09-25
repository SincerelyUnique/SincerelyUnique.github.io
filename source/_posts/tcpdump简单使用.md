---
title: tcpdump简单使用
date: 2021-03-04 17:06:09
tags:
- tools
- network
categories:
---

```raw
1.基本命令
（1）列出可监控的网卡接口（any包含所有active的接口）
	tcpdump --list-interfaces
	tcpdump -D
（2）监控any接口且只输出前5条记录（-c参数-count）
	tcpdump -i any -c 5
（3）默认tcpdump会将ip/port解析为name，禁用该解析（-n和-nn参数），防止dns查找
	tcpdump -i any -c5 -nn
 
 
 
2.输出说明
tcpdump可以抓取和解析各种不同的协议，如TCP, UDP, ICMP等等。
具体可以参考：http://www.tcpdump.org/manpages/tcpdump.1.html#lbAG
 
以TCP为例，通常是返回下面这种格式：
08:41:13.729687 IP 192.168.64.28.22 > 192.168.64.1.41916: Flags [P.], seq 196:568, ack 1, win 309, options [nop,nop,TS val 117964079 ecr 816509256], length 372
 
说明：
（1）08:41:13.729687：代表接收到该数据包的时间戳（本地时钟）
（2）IP：代表网络层协议，这里指IPV4，如果是IPV6，则显示IP6
（3）192.168.64.28.22：源ip地址与端口
（4）192.168.64.1.41916：目标ip地址与端口
（5）Flags [P.]：TCP flags，这里也可以是组合在一起的值，比如[S.]代表SYN-ACK包
	Value	Flag Type	Description
	S	SYN	Connection Start
	F	FIN	Connection Finish
	P	PUSH	Data push
	R	RST	Connection reset
	.	ACK	Acknowledgment
（6）seq 196:568：这次捕获的数据包包含字节从196字节到568字节（this flow）
（7）ack 1：这里是1，因为这是发送数据的一方。如果是接收数据的一方，这个字段表示该流中的下一个预期字节（比如下一个数据包ack就应该为568）
（8）win 309：代表接收缓冲区中可用的字节数，
     参考：https://www.iana.org/assignments/tcp-parameters/tcp-parameters.xhtml
（9）length 372：数据包长度，payload数据的字节长度，即568 - 372
 
 
 
 
3.筛选数据包
（1）根据协议筛选：如筛选icmp的数据包，可以在另一个终端中尝试使用ping命令测试（ping基于icmp协议）
	tcpdump -i any -c5 icmp
（2）根据主机筛选：抓取与指定主机通信的数据包
	tcpdump -i any -c5 -nn host 54.204.39.132
（3）根据所需服务或端口筛选：比如捕获Web（HTTP）服务相关的数据包或者https 443端口、dns 53端口等
	tcpdump -i any -c5 -nn port 80
（4）根据source ip/hostname筛选：
	tcpdump -i any -c5 -nn src 192.168.122.98
	tcpdump -i any -c5 -nn dst 192.168.122.98
（5）复杂表达式：如source IP为192.168.122.98且service HTTP only或者更复杂一些
	tcpdump -i any -c5 -nn src 192.168.122.98 and port 80
	tcpdump -i any -c5 -nn "port 80 and (src 192.168.122.98 or src 54.204.39.132)"
 
 
 
4.检查包内容
上面的都是检查数据包头（header）信息。
tcpdump提供两个额外标志来查看包内容：
	-x   打印16进制内容
	-A   打印ASCII内容
如： tcpdump -i any -c10 -nn -A port 80
这个对于跟踪http的api调用有些作用，对于加密连接没有太大作用
 
 
 
5.将捕获内容保存到文件
这个很方便，比如我们可以以批处理模式捕获夜里数据包，方便第二天早晨做分析。
比如：	tcpdump -i any -c10 -nn -w webserver.pcap port 80
webserver.pcap是我们保存的文件名字，.pcap后缀代表“packet capture”并且是约定的一种包文件格式。
webserver.pcap是二进制文件，不能以文本方式浏览，需使用命令打开：tcpdump -nn -r webserver.pcap。
 
比如：  tcpdump -nn -r webserver.pcap src 54.204.39.132
持续捕获源ip为54.204.39.132的数据包
 
 
 
 
附：
tcpdump官网：http://www.tcpdump.org/#
tcpdump手册：http://www.tcpdump.org/manpages/tcpdump.1.html
带GUI图形界面：https://www.wireshark.org/  或 https://www.telerik.com/fiddler
 
 
```
