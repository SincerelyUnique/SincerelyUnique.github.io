---
title: 记一次DDOS
date: 2021-08-16 17:36:08
tags:
- ddos
- linux
categories:
- [学习, DevOPS, CloudService]
---

```bash
DDOS说明
 
攻击历史信息：
2021-08-13 21:39:47 攻击流量峰值：5426Mbps， 2021-08-13 23:30:00 ip解封
2021-08-14 10:00:32 攻击流量峰值：3158Mbps， 2021-08-14 12:00:00 ip解封
2021-08-14 15:54:29 攻击流量峰值：3426Mbps， 2021-08-14 17:54:29 ip解封
2021-08-15 09:27:59 攻击流量峰值：4869Mbps， 2021-08-15 11:28:11 ip解封
2021-08-15 15:29:37 攻击流量峰值：13015Mbps，2021-08-15 17:31:16 ip解封
2021-08-16 07:26:29 攻击流量峰值：11188Mbps，2021-08-15 09:27:22 ip解封
2021-08-16 09:32:09 攻击流量峰值：11120Mbps，2021-08-15 11:33:22 ip解封
 
DDOS：
拒绝服务攻击（英语：denial-of-service attack，简称DoS攻击）亦称洪水攻击，是一种网络攻击手法，
其目的在于使目标电脑的网络或系统资源耗尽，使服务暂时中断或停止，导致其正常用户无法访问。
当黑客使用网络上两个或以上被攻陷的电脑作为“僵尸”向特定的目标发动“拒绝服务”式攻击时，称为分布
式拒绝服务攻击（distributed denial-of-service attack，简称DDoS攻击）
 
原因：
这边原因不是很好查，即便追溯到源头也可能只是一（多）台傀儡机，DDOS本身是分布式的且不好追溯的，
我这边具体也不是很清楚为什么会造成（我个人猜测也许是ip随机无差别ddos到我们的服务器了），但是
从这几天来看有可能被针对了（当然也可能不是...），我其实不明白为什么如此频繁，我们这边做的是
公益的开源项目，ddos我们也无利可图（因为之前有听过ddos勒索一些小的游戏企业上缴保护费的新闻）。
 
目前情况：
攻击当晚我已经在后端群里和后端所有开发小伙伴说了，也在寻求解决办法，但是因为对安全这边我自身
是有欠缺的，目前可能这个也只能依赖于云服务商解决（我们的服务器供应商是tencent cloud，他们的
解决方式比较暴力-封ip（谁都不要玩~），当然可能是我们主要没有购买他们的ddos高防包，目前使用的
也只是使用的他们的基础防护-购买云服务器免费自带，瞬间流量达到峰值时IP会封锁，其实建议不要咨
询tencent客服，咨询的结果还是让你购买他们的高防包，其实我们这边不太想花钱了，因为服务本身是
客户的，能省则省，然后看网上说可以做些防护，因为我这边最近也比较忙，其实主要是我对这块不是很熟，
就先按照小伙伴本提供的意见，检查下云服务器的安全组，又做了下iptables拦截，暂时先这样，后续还
待观察，其实安全组和iptables有些设置有重叠，不过iptables做了些limit（针对连接及ICMP相关），ICMP
建议还是都关掉。
我们也曾尝试可能更换弹性IP，但是tencent cloud后面这个选项是灰色的，我们是包年包月固定带宽的，
可能不支持变动ip吧，尤其当前ipv4资源枯竭，也许需要另外花钱更换
 
 
防护：
[root@VM-TEST-ENV]# #打开防火墙（centos7自带，后面禁掉了，改用iptables了）
[root@VM-TEST-ENV]# firewall-cmd --state
[root@VM-TEST-ENV]# systemctl start firewalld.service
[root@VM-TEST-ENV]# firewall-cmd --state
 
[root@VM-TEST-ENV]# service iptables status
[root@VM-TEST-ENV]# yum install -y iptables
[root@VM-TEST-ENV]# yum update iptables
[root@VM-TEST-ENV]# yum install iptables-services
[root@VM-TEST-ENV]# systemctl stop firewalld  # 停止firewalld服务
[root@VM-TEST-ENV]# systemctl mask firewalld  # 禁用firewalld服务
[root@VM-TEST-ENV]# iptables -L -n  # 查看iptables现有规则
[root@VM-TEST-ENV]# iptables -P INPUT ACCEPT  # 先允许所有,不然有可能会杯具
[root@VM-TEST-ENV]# iptables -F  # 清空所有默认规则
[root@VM-TEST-ENV]# iptables -X  # 清空所有自定义规则
[root@VM-TEST-ENV]# iptables -Z  # 所有计数器归0
[root@VM-TEST-ENV]# iptables -A INPUT -i lo -j ACCEPT  # 允许来自于lo接口的数据包(本地访问)
[root@VM-TEST-ENV]# iptables -A INPUT -p tcp --dport 22 -j ACCEPT  # 开放22端口
[root@VM-TEST-ENV]# iptables -A INPUT -p tcp --dport 21 -j ACCEPT  # 开放21端口(FTP)
[root@VM-TEST-ENV]# iptables -A INPUT -p tcp --dport 80 -j ACCEPT  # 开放80端口(HTTP)
[root@VM-TEST-ENV]# iptables -A INPUT -p tcp --dport 443 -j ACCEPT # 开放443端口(HTTPS)
[root@VM-TEST-ENV]# iptables -A INPUT -p icmp --icmp-type 8 -j ACCEPT   #允许ping
[root@VM-TEST-ENV]# iptables -A INPUT -m state --state  RELATED,ESTABLISHED -j ACCEPT   # 允许接受本机请求之后的返回数据 RELATED,是为FTP设置的
[root@VM-TEST-ENV]# iptables -P INPUT DROP  # 其他入站一律丢弃
[root@VM-TEST-ENV]# iptables -P OUTPUT ACCEPT  # 所有出站一律绿灯
[root@VM-TEST-ENV]# iptables -P FORWARD DROP   # 所有转发一律丢弃
 
[root@VM-TEST-ENV]# #防止SYN攻击 轻量级预防
[root@VM-TEST-ENV]# iptables -N syn-flood
[root@VM-TEST-ENV]# iptables -A INPUT -p tcp --syn -j syn-flood
[root@VM-TEST-ENV]# iptables -I syn-flood -p tcp -m limit --limit 3/s --limit-burst 6 -j RETURN
[root@VM-TEST-ENV]# iptables -A syn-flood -j REJECT
 
[root@VM-TEST-ENV]# #防止DOS太多连接进来,可以允许外网网卡每个IP最多15个初始连接,超过的丢弃
[root@VM-TEST-ENV]# iptables -A INPUT -i eth0 -p tcp --syn -m connlimit --connlimit-above 15 -j DROP
[root@VM-TEST-ENV]# iptables -A INPUT -p tcp -m state --state ESTABLISHED,RELATED -j ACCEPT
 
[root@VM-TEST-ENV]# #用Iptables抵御DDOS (参数与上相同)
[root@VM-TEST-ENV]# iptables -A INPUT -p tcp --syn -m limit --limit 12/s --limit-burst 24 -j ACCEPT
[root@VM-TEST-ENV]# iptables -A FORWARD -p tcp --syn -m limit --limit 1/s -j ACCEPT
 
[root@VM-TEST-ENV]# 控制单个IP的最大并发连接数-允许单个IP的最大连接数为 30
[root@VM-TEST-ENV]# iptables -I INPUT -p tcp --dport 80 -m connlimit --connlimit-above 50 -j REJECT
 
[root@VM-TEST-ENV]# service iptables save
[root@VM-TEST-ENV]# systemctl enable iptables.service
[root@VM-TEST-ENV]# systemctl start iptables.service
[root@VM-TEST-ENV]# systemctl status iptables.service
[root@VM-TEST-ENV]# systemctl restart iptables.service
 
 
参考：
https://www.cnblogs.com/kreo/p/4368811.html
https://cloud.tencent.com/developer/article/1370824
https://gist.github.com/mattia-beta/bd5b1c68e3d51db933181d8a3dc0ba64
 
 
1 天 后 ~~~
 
 
更新：
上述配置后过了1天后的上午，很杯具的又遇到腾讯云的 ddos 攻击通知，
然后检查了下安全组并在安全组固定了一下开放22和3306端口的ip网段，
并且仔细查看了下 ddos 记录，看到是UDPFLOOD攻击，好吧，暂时想不到
办法了~~
 
 
多天后
 
 
这几天持续被ddos攻击，和腾讯云技术人员有沟通，没有太多效果，对方认为我们服务器遭到的攻击
不是那种专业黑客操作的（原因是其无规律性 和 流量峰值并不高），还是建议我们购买他们的高防
包，我顺便问了下他们高防包的具体实现和做法，他们说使用高防包后其实就是从两方面着手，一种
是流量过滤（恶意流量侦测，对冲给刷掉，这个属于流量管控方面），另外就是增加带宽（扩充现有
带宽，增加抗压能力），但是实在是太贵了，客服有截图给我，3个月1w8，而且只是10次ddos的防御，
也就是3个月内防御次数受限，只能防御10次，而我们一周就不下10次了，肯定不划算而且解决不了
实际问题，另外一种就是推荐我们购买高防ip，但是高防ip一个月好像是6000+左右，客服说因为我们
是公益项目可以帮忙申请折扣，但是想了想，即便有折扣的情况下还是很贵，我们不是商业项目，也没有
盈利什么的，反而会让公益项目雪上加霜。
腾讯云那边暂时提议（其实我们也在准备做的）更换ip，但是更换ip有个要注意的地方，就是尽可能不
要频繁更换ip，还有就是不要换一个被攻击一个，这种情况腾讯云会认为我们恶意更换导致ip被攻击，
会导致我们的信誉降低因为不用的ip会进入回收池，在当前ipv4枯竭情况下，ip资源显得越来越重要，
如果我们换过的ip都频繁被攻击，那么他们也无法再利用。
 
 
1 天 后 ~~~
 
 
换了ip后大致过了1个小时左右，杯具又发生了，ip遭到ddos，其实我也不大理解为什么，ip刚刚更换
完，我甚至都没有将ip告知其他开发人员，这种情况就发生了，我甚至怀疑自己的电脑是不是被黑掉了
导致我的tencent cloud账号泄漏，但是如果账号泄漏，黑客直接就可以直接拿到我们server做各种事
情了，根本没必要再ddos我们，就是很奇怪~~
 
 
1 天 后 ~~~
 
 
我们又有了一些猜测，也许是根据域名攻击，然后我们重新做了下域名解析，随便找了个我自己的vps
绑定该域名，也就是拿我自己的vps当靶子，验证下是否域名上的ddos攻击
 
2 天 后 ~~~
 
 
更换域名解析后1天还是很安静的，本以为就这样安安静静的过去，没想到第二天又收到腾讯云的ip ddos
告警

至今没有彻底解决，攻击频率比较低了，至少最近十多天没有攻击的警告~
```
