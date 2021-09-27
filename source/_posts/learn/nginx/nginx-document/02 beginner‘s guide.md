---
title: (2) Beginner‘s guide
date: 2020-12-26 00:07:05
categories:
- [学习, Nginx学习, Nginx文档学习]
tags:
- nginx
---

```
概述：
nginx发音：engine X
nginx作者：伊戈尔·赛索耶夫 （俄国）
nginx作用：网页服务器，反向代理、负载均衡、web缓存
发布时间：2004 （2011成立同名公司，2019年被F5 Networks以6.7亿美元收购）
开源软件协议：BSD
Github项目活跃度：活跃
编写语言：C
处理请求方法：异步事件驱动（异步框架）
并行度估值：官方测试可支持5w并行连接，实际支持2w~4w
优点：占有内存少、并发能力强、模块化设计、配置灵活、epoll&kqueue
缺点：低并发场景性能与Apache相当（或低于）、性能提升导致灵活性降低、整体稳定性稍逊于apache
负载均衡：rr轮询（default）、rr+weight（权重）、ip_hash、fair、url_hash、least_conn、consitent_hash
负载均衡多台服务器间session共享：使用cookie、session集群、ip_hash
官方文档：http://nginx.org/en/docs/
 
nginx由1个master进程和多个worker进程组成：
master进程主要读取和检测配置，并管理worker进程
worker进程处理实际请求
nginx采用基于事件的模型和依赖于操作系统的机制来有效地在worker进程间分配请求
- event-based model
- OS-dependent mechanisms
worker进程数量可以通过配置文件配置，也可以自动按照cpu内核数量自动调整
（http://nginx.org/en/docs/ngx_core_module.html#worker_processes）
 
 
1.help命令：
[root@jalen nginx]# nginx -help
nginx version: nginx/1.19.6
Usage: nginx [-?hvVtTq] [-s signal] [-p prefix]
             [-e filename] [-c filename] [-g directives]
 
Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /etc/nginx/)
  -e filename   : set error log file (default: /var/log/nginx/error.log)
  -c filename   : set configuration file (default: /etc/nginx/nginx.conf)
  -g directives : set global directives out of configuration file
 
 
2.nginx信号操作（signal）
nginx -s stop — 快速关闭
nginx -s quit — 正常关闭，会等待worker进程处理完当前的request后关闭（需和start操作隶属于同一用户）
nginx -s reload — 重新加载配置文件，主进程接收后做语法校验，
                  校验成功：开启新的worker进程并关闭老的worker进程，关闭老进程时会先暂停接收新的request，
                  直到当前所有request结束后才能关闭。此时可以发现老的worker pid会变化。
                  校验失败：rollback回老的配置
nginx -s reopen — 重新打开日志文件，日志分割
 
 
3.启动nginx，直接输入nginx
[root@jalen nginx]# nginx                   --方式一
[root@jalen nginx]# service nginx start     --方式二
[root@jalen nginx]# systemctl start nginx   --方式三
 
 
4.查看nginx运行状态
[root@jalen nginx]# service nginx status
[root@jalen nginx]# systemctl status nginx
 
 
5.停止nginx
[root@jalen nginx]# nginx -s stop           --方式一
[root@jalen nginx]# service nginx stop      --方式二
[root@jalen nginx]# systemctl stop nginx    --方式三
 
 
6.查看nginx进程
[root@jalen local]# ps -ax | grep nginx
[root@jalen local]# ps -efl|grep nginx
 
 
7.nginx配置文件结构
nginx由配置文件中指定的指令控制各个模块。指令分为简单指令和块指令。
简单指令格式：指令名称 + 空格 + 参数 + 分号结尾
块指令格式（简单指令前有4格缩进）：指令名称 + 空格 + { + 简单指令 + }
#号后面是注释
块指令内所有指令被称为context上下文，在所有contexts外部的指令视为main context
 
 
8.提供静态内容
Web服务器的一项重要任务是分发文件，像图片和静态html页面
（1）nginx.conf配置如下，默认80端口：
    server {
        location / {
            root /data/www;
        }
        location /images/ {
            root /data;
        }
    }
（2）执行下面指令创建文件夹并放入静态文件，浏览器访问http://ip/images/example.jpg
[root@jalen images]# cd /usr/share/nginx/html
[root@jalen images]# mkdir images
[root@jalen images]# cd images/
[root@jalen images]# wget http://xxx.xxx.xxx/000.jpg
[root@jalen images]# mv 000.jpg example.jgp
[root@jalen images]# nginx -s reload
 
 
9.设置简单的代理服务器
request：client -> nginx server -> proxy server
response: proxy server -> nginx server -> client
（1）/nginx/html/data/up1路径下创建index.html
（1）配置：proxy server-8080端口， nginx server-80端口，client访问80端口会走到8080
    server {
        listen 8080;
        root /data/up1;
        location / {
        }
    }
    server {
        location / {
            proxy_pass http://localhost:8080;
        }
 
        location ~ \.(gif|jpg|png)$ {
            root /data/images;
        }
    }
（2）测试：http://104.243.21.35/data/up1/index.html， 访问非图片信息会走代理
 
 
 
10.配置fastcgi代理
此时使用fastcgi_pass指令而不是proxy_pass，比如PHP应用。
使用fastcgi_param指令设置参数
http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#fastcgi_pass
http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#fastcgi_param
如对PHT设置（除静态图片外的request走proxy）：
server {
    location / {
        fastcgi_pass  localhost:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param QUERY_STRING    $query_string;
    }
 
    location ~ \.(gif|jpg|png)$ {
        root /data/images;
    }
}
 
 
 
11.检测nginx配置是否正确
[root@jalen sbin]# ./nginx -t
nginx: the configuration file /opt/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /opt/nginx/conf/nginx.conf test is successful
[root@jalen sbin]# 
 
 
 
12.隐藏nginx版本号（防止版本漏洞攻击: 在nginx.conf的http节点下添加server_tokens off）
root@xxxx:~# curl -I www.test.com
HTTP/1.1 301 Moved Permanently
Server: nginx/1.10.3
Date: Tue, 30 Nov 2020 02:35:40 GMT
Content-Type: text/html
Content-Length: 185
Connection: keep-alive
Location: https://www.test.com/
 
 
 
13.禁止curl：用户在curl某个页面时，统一返回404
if ($http_user_agent ~*"curl"){
    return 404;
}
 
 
 
14.防盗链
location ~* \.(gif|jpg|swf)$ {
    valid_referers none blocked start.iyunv.cn sta.iyunv.cn;
    if ($invalid_referer) {
        rewrite ^/ http://$host/logo.png;
    }
}
 
 
 
15.限制访问：禁止访问某个目录
location ~* \.(txt|doc)${
    root /data/www/wwwroot/iyunv/test;
    deny all;
}
location ~ ^/images/.*\.(php|php5|.sh|.pl|.py)$
{
    Allow 127.0.0.1;
    deny all;
}
location / {
    deny 192.168.1.1;
    allow 192.168.1.0/24;
    allow 10.1.1.0/16;
    deny all;
}
if ( $remote_addr = 10.0.0.7 ) {
    return 403;
}
if ($http_user_agent ~* "Firefox|MSIE") {
    return 403;
    rewrite ^(.*) http://blog.etiantian.org/$1 permanent;
}
 
 
 
 
16.负载均衡
upstream proxy_nginx {
    server 192.168.0.254 weight=1 max_fails=2 fail_timeout=10s ;
    server 192.168.0.253 weight=2 max_fails=2 fail_timeout=10s;
    server192.168.0.252 backup；
    server192.168.0.251 down；
}
server{
    listen 80;
    server_name a.com;
    location / {
        proxy_pass http:// proxy_nginx;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
 
 
 
17.gzip压缩
gzip on;
 
 
 
18.缓存
location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
    expires 30d;
}
location ~.*\.(js|css|javascript|fluash)$ {
    expires 24h;
}
 
 
说明：12-18转自iyunv的Nginx从入门到精通.pdf，未验证
 
 
其他：
1.service xxx start/stop/restart理解
service其实是一个/sbin/下的一个脚本，名称为service，可以使用vi打开这个脚本查看，重点注意其中几个变量
（1）${ACTION}
脚本中有段说明：The service command supports only basic LSB actions (start, stop, restart, try-restart, 
reload, force-reload, status). For other actions, please try to use systemctl
也就是说目前centos7使用service仅支持这些action：start, stop, restart, try-restart, reload, force-reload, status
对于其他action，还是要使用systemctl来执行。
 
（2）$PATH
可执行路径，也就是系统的环境变量，可以这样查看
[root@jalen sbin]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
[root@jalen sbin]#
 
（3）$TEEM
[root@jalen sbin]# echo $TERM
xterm
[root@jalen sbin]#
XTerm是linux终端模拟器，其他还有一些比如Rxvt、Konsole、Kvt、Gnome-Terminal、Nxterm、eterm。
Xterm很古老，1984年出生，Xterm是X Window系统的默认终端模拟器，是非常简单的一个终端，非常小，MIT许可。
测试xterm可以简单输入一个top命令，然后按键盘z，可以看到终端变色，这就是xterm的特性之一。
（参考：https://www.oschina.net/translate/top-open-source-terminal-emulators）
 
（4）SERVICEDIR="/etc/init.d"
具体可以看下/etc/init.d/README，里面有详细说明该目录作用
You are looking for the traditional init scripts in /etc/rc.d/init.d,
and they are gone?
 
Here's an explanation on what's going on:
 
You are running a systemd-based OS where traditional init scripts have
been replaced by native systemd services files. Service files provide
very similar functionality to init scripts. To make use of service
files simply invoke "systemctl", which will output a list of all
currently running services (and other units). Use "systemctl
list-unit-files" to get a listing of all known unit files, including
stopped, disabled and masked ones. Use "systemctl start
foobar.service" and "systemctl stop foobar.service" to start or stop a
service, respectively. For further details, please refer to
systemctl(1).
 
Note that traditional init scripts continue to function on a systemd
system. An init script /etc/rc.d/init.d/foobar is implicitly mapped
into a service unit foobar.service during system initialization.
 
Thank you!
 
Further reading:
        man:systemctl(1)
        man:systemd(1)
        http://0pointer.de/blog/projects/systemd-for-admins-3.html
        http://www.freedesktop.org/wiki/Software/systemd/Incompatibilities
 
 
 
2.linux添加用户和切换用户
[root@jalen ~]# adduser jalen
[root@jalen ~]# adduser kitty
[root@jalen ~]# su - jalen
 

```
