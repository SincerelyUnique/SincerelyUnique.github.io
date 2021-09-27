---
title: 记nginx upstream timed out一种情况
date: 2020-12-16 22:04:09
tags:
- nginx
categories:
---

```
1.问题描述
前端vue访问nginx，nginx访问后端，发现前端请求需要等待很久才能到达后端
接口，并且nginx存在upstream timed out错误log。
（1）nginx配置（version：1.15.8）
    server {
        listen       8989;
        server_name  localhost;
		
        location / {
          proxy_pass http://localhost:9999;
        }
 
        location /api/ {
          proxy_pass http://localhost:8080/;
          proxy_read_timeout      500;
        }
		
	    # ... ...
     }
（2）nginx日志（nginx-1.15.8\logs\error.log）
2020/12/16 07:23:59 [error] 2592#13852: *1 upstream timed out (10060: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond) while connecting to upstream, client: 127.0.0.1, server: localhost, request: "GET /feature/index HTTP/1.1", upstream: "http://[::1]:9999/feature/index", host: "localhost:8989"
2020/12/16 07:25:05 [error] 2592#13852: *8 upstream timed out (10060: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond) while connecting to upstream, client: 127.0.0.1, server: localhost, request: "POST /api/feature/releasedfeature HTTP/1.1", upstream: "http://[::1]:8080/feature/releasedfeature", host: "localhost:8989", referrer: "http://localhost:8989/feature/index"
 
通过网络搜索nginx upstream timed out问题看到一篇博客，结合日志中(比如
client: 127.0.0.1, server: localhost..以及[::1]）猜测上面描述的问题有
可能是ipv6对localhost的解析问题导致，而在我的nginx配置里，大部
分地址都是采用localhost配置的，比如server_name配置为localhost，或者
location代理路径url也是填写的localhost，故而尝试将nginx配置文件中所有
localhost修改为127.0.0.1，修改后重启nginx，发现前端访问能够很快到达后端，
不会再出现延迟的情况了。
 
但是后续偶尔还是会在log里发现类似error，log如下：
2020/12/16 12:00:58 [error] 13424#25016: *7 upstream timed out (10060: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond) while reading response header from upstream, client: 127.0.0.1, server: 127.0.0.1, request: "POST /api/feature/sp HTTP/1.1", upstream: "http://127.0.0.1:8080/feature/sp", host: "localhost:8989", referrer: "http://localhost:8989/feature/fed"
可以看到这条log的client和server都是127.0.0.1：client: 127.0.0.1, server: 127.0.0.1
但是host却是localhost的：host: "localhost:8989"（这个有些奇怪，估计哪里用了localhost没有修改，以后再查吧）
 
这个猜测也可能具体和nginx的实现有些关系，比如有可能app不支持ipv6访问，所以nginx访问超时了，超时之后
nginx又尝试使用IPv4访问，然后正常了
 
或者也可以尝试配置nginx ipv6 listen（未尝试），如：
listen 80;
listen [::]:80 ipv6only=on;
 
 
 
2.验证localhost在ipv4环境和ipv6环境下的不同
（1）在ipv4网络环境中使用ping命令ping本地执行返回的是（ipv6切换ipv4参考下面附录）：
     C:\Users\jalchu>ping localhost
     Pinging JALCHU-6JT6N.xxx.com [127.0.0.1] with 32 bytes of data:
     Reply from 127.0.0.1 time<1ms
     Reply from 127.0.0.1 time=1ms
     Reply from 127.0.0.1 time<1ms
     Reply from 127.0.0.1 time=1ms
 
     Ping statistics for 127.0.0.1:
         Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
     Approximate round trip times in milli-seconds:
         Minimum = 0ms, Maximum = 1ms, Average = 0ms
     C:\Users\jalchu>
（2）在ipv6网络环境中使用ping命令ping本地执行返回的是：
     C:\Users\jalchu>ping localhost
     Pinging JALCHU-6JT6N.xxx.com [::1] with 32 bytes of data:
     Reply from ::1: time<1ms
     Reply from ::1: time=1ms
     Reply from ::1: time<1ms
     Reply from ::1: time=1ms
 
     Ping statistics for ::1:
         Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
     Approximate round trip times in milli-seconds:
         Minimum = 0ms, Maximum = 1ms, Average = 0ms
     C:\Users\jalchu>
（3）由上可知在两个网络环境下localhost解析是不同的，ipv4指向127.0.0.1，
     而ipv6指向::1:，也就是说localhost在ipv4环境下解析后的loopback address
     是 127.0.0.1，在ipv6环境下解析后的loopback address是::1。
 
     在计算机网络中，localhost是一个主机名，它用于访问它的当前计算机。
     通过环回网络接口访问主机上运行的网络服务。使用该接口可绕过各种本
     地网络接口硬件（即无需物理网络接口，无需网络服务，就像我们可以
     直接通过http://localhost从浏览器访问页面一样）。--wiki
 
     ipv4 loopback address 为127.0.0.0/8 （A类地址），eg：127.0.0.1
     ipv6 loopback address 为::1/128，eg：::1:
 
 
 
3.IPV4简介
ipv4：第四代网络协议，这个协议第一个版本应用于1982年的SATNET和1983年的ARPANET，
它使用32位地址空间，提供了4,294,967,296（2的32次方，约43亿）个唯一地址，并且为
一些专用网络保留一部分特殊的地址块，如：
（1）private networks（LAN）：1800w
（2）multicast（多播） addresses：27000w
 
ip地址空间由IANA管理，指定由5个RIR（regional Internet registries）负责分配给
终端用户或者下级ISP供应商，加速IPv4地址耗尽的主要原因包括迅速增长的Internet用户
及始终在线的设备（包括各种移动设备）等，地址耗尽时间（或者可以理解为每个RIR ip 
address pool的耗尽（预期）时间）：
（*）Global level（全球级别-顶级）：2011.1.31
（1）Asia-Pacific (APNIC)： 2011.4.15
（2）Latin America and the Caribbean (LACNIC)：2014.6.10
（3）North America (ARIN)：2015.9.24
（4）Africa (AfriNIC)：2017.4.21
（5）Europe, Middle East and Central Asia (RIPE NCC)：2019.11.25
当然，这些RIR仍然可以分配已恢复的地址，或者因为特殊目的而保留的地址。
（各个ISP仍具有未分配的IP地址池，并且可以回收用户不再需要的地址）
 
 
 
4.IPV5简介
介于IPv4和IPv6之间，1979年将版本5分配给实验性Internet流协议，
曾经打算替代IPV4，但是事实上从未真正履行甚至并不将其称之为IPv5。
IPv5又称因特网流协议，是一个资源协议，即因特网流协议(ST)。
该协议从未被接受为正式的互联网协议，这主要是由于 32 位限制。
IPV5 使用与 IPv4 相同的寻址系统。每个地址由 0 到 255 之间的四组数字组成。
这将可能的地址数量限制为 43 亿个。
但是IPv5 奠定了 VoIP 的基础，而该技术已被我们用于当今世界范围内的通信，在某种
程度上，可以说 IPv5 仍然保留到了今天。
 
 
 
5.IPV6简介
IPV6是网际协议的最新版本，主要用它来取代IPv4主要是为了解决IPv4地址枯竭
问题，同时它也在其他方面对于IPv4有许多改进（更确切讲是对IPv4的一种保守的扩展）。
比如使用新的分组格式，最小化路由器处理的消息标头（IPv4消息和IPv6消息标头有很大不同，
无法互操作）、扩大地址空间等。
 
IPv6采用128位的地址（区别于ipv4的32位），IPv6地址的表达形式一般采用32个十六进制数。
 
在很多场合，IPv6地址由两个逻辑部分组成：一个64位的网络前缀和一个64位的主机地址，
主机地址通常根据物理地址自动生成，叫做EUI-64（或者64-位扩展唯一标识）。
 
IPv6地址可分为三种：单播（unicast）地址、任播（anycast）地址、多播（multicast）地址
 
在2019年12月，透过IPv6使用Google服务的用户百分率首次超过30%，正符合最后一个RIR
宣布资源枯竭的时间。
 
 
 
 
附录
1.windows10设置ipv4的优先级
win10默认的是ipv6的解析，而ipv6默认解析到[::1]而不是127.0.0.1，可以去注册表修改ipv4的优先级
（1）cmd输入regedit进入注册表
（2）HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\tcpip6\Parameters，右键
     Parameters新建<DWORD(32位)值(D)>，名称修改为DisabledComponents，数据改为20
（3）重启电脑后尝试ping localhost
 
 
 
参考：
https://blog.csdn.net/u010267491/article/details/52775115 （搜索nginx upstream问题定位到这篇博客）
https://en.wikipedia.org/wiki/IPv4 （ipv4简介）
https://tools.ietf.org/html/rfc791 （ipv4描述）
https://en.wikipedia.org/wiki/Private_network  （私有网络简介）
https://en.wikipedia.org/wiki/Multicast （网络多播）
https://www.jianshu.com/p/164ab8a6e6a9 （网络单播、多播、广播区别参考）
https://tools.ietf.org/html/rfc1918 （私有网络地址分配参考）
https://tools.ietf.org/html/rfc4193 （ipv6地址）
https://en.wikipedia.org/wiki/Localhost （wiki对localhost的解释）
https://en.wikipedia.org/wiki/IPv4_address_exhaustion （ipv4地址耗尽）
https://zh.wikipedia.org/wiki/IPv4%E4%BD%8D%E5%9D%80%E6%9E%AF%E7%AB%AD （ipv4地址枯竭）
https://zhuanlan.zhihu.com/p/137385830 （为什么没有ipv5）
https://zh.wikipedia.org/wiki/IPv6 （IPV6）
https://www.google.com/intl/en/ipv6/statistics.html （google ipv6使用率统计）
 
 
 
```
