---
title: 504 Gateway Time-out(nginx)
date: 2020-07-21 15:53:57
tags:
- nginx
categories:
---

nginx 504 Gateway Time-out

场景：前端vue请求后端超时

原因：后端dao层执行sql查询速度慢，导致nginx无法在默认的时间内（60s）从后端获取response

解决：在http > server > location下面添加proxy_read_timeout属性，延长时间，单位为秒，如设为5min

```nginx
        location /api/ {
          proxy_pass http://localhost:8080/;
          proxy_read_timeout      300;   # 设置为5分钟
        }
```

```
proxy_read_timeout time
Link：http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_read_timeout
Syntax: proxy_read_timeout time;
Default: proxy_read_timeout 60s;
Context: http, server, location
 
Defines a timeout for reading a response from the proxied server. 
The timeout is set only between two successive read operations, 
not for the transmission of the whole response. If the proxied server 
does not transmit anything within this time, the connection is closed.
```

参考：

https://blog.csdn.net/ruijiao_ren/article/details/79499228

http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_read_timeout

https://stackoverflow.com/questions/24453388/nginx-reverse-proxy-causing-504-gateway-timeout

附录：

> windows启动nginx：start nginx.exe
>
> windows关闭nginx：nginx.exe -s quit

```
proxy_connect_timeout
Link: http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_connect_timeout
Syntax:    proxy_connect_timeout time;
Default: proxy_connect_timeout 60s;
Context: http, server, location
 
Defines a timeout for establishing a connection with a proxied server. 
It should be noted that this timeout cannot usually exceed 75 seconds.
```

```
proxy_send_timeout
Link: http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_send_timeout
Syntax:    proxy_send_timeout time;
Default: proxy_send_timeout 60s;
Context: http, server, location
 
Sets a timeout for transmitting a request to the proxied server. 
The timeout is set only between two successive write operations, 
not for the transmission of the whole request. If the proxied server 
does not receive anything within this time, the connection is closed.
```
