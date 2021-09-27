---
title: nginx和vue前端简单部署记录
date: 2020-12-01 15:33:57
tags:
- nginx
- vue
categories:
---

```
简单部署nginx
 
 
1. 部署nginx，公司内部源有nginx的rpm包，yum install nginx
nginx官方yum安装说明：https://nginx.org/en/linux_packages.html#RHEL-CentOS
yum源没有的话可以从网上下载源码，sftp上传到服务器，选择编译安装（或者自己添加yum源）：
各版本下载地址：https://nginx.org/en/download.html
下载：wget https://nginx.org/download/nginx-1.18.0.tar.gz
解压：tar -zxvf nginx-1.18.0.tar.gz
 
2. 打包前端vue项目(from 花裤衩github)
打包：npm run build:prod
sftp上传dist目录内容(webstorm)：
Tools->Deployment->Configuration添加server，
Tools->Deployment->Browser Remote Host->右侧边栏显示Remote Host
点击Remote Host进入server，选择想要上传的目录，直接将dist目录从webstorm拖拽到server目录
 
3. nginx启动后访问页面空白，F12控制台报下面错误，请求被CSP拒绝
公司内部nginx新版本1.18添加了header，注释掉即可
Refused to apply inline style because it violates the following Content Security Policy 
directive: ... ... default-src 'self'
注释nginx配置（/nginx/conf/nginx.conf）：
    #client_max_body_size 100K;
    #large_client_header_buffers 2 1k;
    #limit_conn_zone $binary_remote_addr zone=limitperip:10m;
    #add_header X-Content-Type-Options "nosniff";
    #add_header X-Xss-Protection "1; mode=block";
    #add_header Content-Security-Policy "default-src 'self'";
    #add_header Referrer-Policy "no-referrer";
 
 
参考：
http://nginx.org/en/docs/http/ngx_http_headers_module.html#add_header  （nginx启动CSP策略问题）
```
