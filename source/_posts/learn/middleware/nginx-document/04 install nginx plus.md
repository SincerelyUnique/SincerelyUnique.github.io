---
title: (4) Install nginx plus
date: 2021-01-03 19:20:27
categories:
- [学习, 中间件学习, Nginx文档学习]
tags:
- nginx
---

```
参考：https://docs.nginx.com/nginx/admin-guide/
 
 
1.nginx plus简介
相较于nginx添加了众多企业特性，如HA，health check，DNS发现，session持久化，
restful API等，是一个云原生、易于使用的反向代理（或负载均衡器和API网关）
特点：
（1）实时监控：DDoS攻击检测，网站error report和性能metrics展示，API支持等
     参考：https://www.nginx.com/products/nginx/live-activity-monitoring
（2）安全升级：现代化的应用安全解决方案，无缝集成节省时间，优于其他的WAF，
               降低系统复杂度，基于成熟的F5应用安全技术，高可信度签名，实现
               极低的误报率，并集成第三方解决方案，如JWT认证等
     参考：https://www.nginx.com/products/nginx-app-protect/
（3）编排k8s容器：NGINX Ingress Controller是Kubernetes和容器化环境中针对云
                  原生应用的一流流量管理解决方案。All-in-One、高性能，可伸缩、
                  安全，CNCF调查显示三分之二受访者使用NGINX Ingress Controller，
                  并且其在DockerHub下载量达千万次。
     参考：https://www.nginx.com/products/nginx-ingress-controller/
 
 
2.安装nginx plus
Nginx plus可以安装在Amazon Linux、CentOS、Debian、FreeBSD、Oracle Linux、RHEL、SLES和Ubuntu。
（1）前提
nginx plus可以申请试用或者直接订阅，
申请试用地址：https://www.nginx.com/free-trial-request  （填写表单提交）
等待几分钟后会收到nginx激活团队的欢迎使用的说明性文件，其中包含配置说明，admin guide
以及support email等link，我是2020-12-28 01:41(GMT)收到的第一封邮件（在表单提交后几分钟内），
邮件标题：Welcome to your free trial of NGINX Plus!
说明：You will soon receive an email from our evaluations team prompting you to activate your trial.
 
第二封激活邮件是2020-12-29 02:39(GMT)收到的，大概等了一天，里面包含激活link，以及安装配置说明。
link是一次性的，点进去accept条款后会跳转到证书和私钥下载page，注意下载也是一次性的。
下载包括：nginx-repo.crt（证书）， nginx-repo.key（私钥）
邮件标题：Activate Your NGINX Plus Trial
激活link说明：This link will expire after it is clicked
下载说明：Your certificate and key can be downloaded once
激活后安装手册：
   https://cs.nginx.com/repo_setup  （手动安装说明）
   https://cs.nginx.com/t/xxxxxxxxxxxxxxxxxxxxxxx/otl   （脚本自动安装指引）
上面第二个链接是份脚本安装指令，可以通过该脚本指令直接在centos上安装nginx plus，该脚本附带一个唯一
的hash值（即下面的317ab16e38c0f1ba95d0a79aee63481c），说明其存在hash校验，也即该指令也是一次性的，第二次
使用同一hash值去安装会报证书404 not found error。
脚本安装：
[root@jalen jalen]# mkdir nginx-plus
[root@jalen jalen]# cd nginx-plus/
[root@jalen nginx-plus]# wget https://cs.nginx.com/static/install-nginx && sudo chmod +x install-nginx
[root@jalen nginx-plus]# sudo ./install-nginx 317ab16e38c0f1ba95d0a79aee63481c
[root@jalen nginx-plus]# nginx -v
nginx version: nginx/1.19.5 (nginx-plus-r23)
 
 
3.其他安装
（1）安装nginx开源版，开源版分为稳定版和主线版：
主线版（mainline）：包含最新特性和bug修复，是最新的，不过可能包含一些实验模块，也可能会有些新bug。
稳定版（stable）：不包含最新特性，且一些重要的bug会被修复，建议用于生产服务器。
参考：https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/#
（2）在Amazon EC2安装Nginx plus AMIs
参考：https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-plus-amazon-web-services/
（3）在Google云平台安装Nginx plus
参考：https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-plus-google-cloud-platform/
（4）在微软Azure上安装Nginx plus
参考：https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-plus-microsoft-azure/
（5）在Docker容器内安装Nginx和Nginx Plus
参考：https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-docker/
 
 
4.基本功能描述
（1）在运行时控制nginx进程
主进程和worker进程：nginx有1个master进程和1个或多个worker进程，如果启用了cache，cache loader和cache
manager进程在启动时也会运行。主进程的目的是读取和评估配置文件，同时管理worker进程。worker进程处理实际
requests。nginx依赖于系统机制在工作进程之间高效分发请求。worker进程数量由nginx.conf文件里的
worker_processes指令设定，可以设置成和CPU cores数量一致。
 
（2）控制nginx
nginx -s <SIGNAL>
<SIGNAL>可以是下面几种：
quit：优雅地关闭
reload：重新加载配置文件
reopen：重新打开日志文件，用于日志切割
stop：立即关闭（快速关闭）
也可以用kill命令操作nginx主进程pid向主进程发送信号，主进程号查询：
[root@jalen sbin]# ps -efl|grep 'master process nginx'
 
（3）指令
single‑line指令，分号结束；
多行指令一般包含大括号，括号视为容器，容器内指令视为上下文context；
 
（4）特定功能的配置文件
为了方便管理配置，建议将配置拆分为一组分别处理特定功能的文件存储在/etc/nginx/conf.d目录下，在
nginx.conf主配置文件中使用include指令指向这些文件，如：
include conf.d/http;
include conf.d/stream;
include conf.d/exchange-enhanced;
 
（5）contexts上下文
针对top-level指令，即块指令（包含大括号的指令），如：
events – General connection processing
http – HTTP traffic
mail – Mail traffic
stream – TCP and UDP traffic
其他single-line指令默认被包含在称为main context的context内。配置文件例子：
 
user nobody; # a directive in the 'main' context
 
events {
    # configuration of connection processing
}
 
http {
    # Configuration specific to HTTP and affecting all virtual servers  
 
    server {
        # configuration of HTTP virtual server 1       
        location /one {
            # configuration for processing URIs starting with '/one'
        }
        location /two {
            # configuration for processing URIs starting with '/two'
        }
    } 
    
    server {
        # configuration of HTTP virtual server 2
    }
}
 
stream {
    # Configuration specific to TCP/UDP and affecting all virtual servers
    server {
        # configuration of TCP virtual server 1 
    }
}
 
 
 
 
备注：
1.WAF：
Web Application Firewall，包括但不限于SQLi、XSS、CSRF、暴力破解、文
件上传、RFI等常见Web应用攻击的防护，拥有自学习模式和自定义规则）
 
 
2.F5：
传统WAF的主要模式依然利用协议合规和签名比对来防范攻击，局限于通过检测
请求、流量的形态来判断是否恶意，F5不仅从网络层面注意安全，更从应用层面
进行了一系列保护，如：识别流量、凭证安全、应用层DoS防御、灵活配置。
参考：https://www.aqniu.com/tools-tech/34690.html
 
 
3.CNCF：
全称Cloud Native Computing Foundation（云原生计算基金会）， 2015.7.21成立，
口号是坚持和整合开源技术来让编排容器作为微服务架构的一部分，厂商中立，致力
于如Kubernetes、Prometheus、Envoy等Github上的快速成长的开源技术的推广。
参考：https://jimmysong.io/kubernetes-handbook/cloud-native/cncf.html
 
 
4.卸载nginx
[root@jalen ~]# nginx -s quit
[root@jalen ~]# sudo yum remove nginx
```
