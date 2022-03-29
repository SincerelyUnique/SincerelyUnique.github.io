---
title: Grafana设置为Iframe
date: 2015-02-22 14:34:43
tags: grafana
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 设置可嵌入iframe

找到grafana.ini, 

security:
   allow_embedding: true

参考： https://grafana.com/docs/grafana/v7.5/administration/configuration/#allow_embedding  （将grafana嵌入iframe）


# 设置免密登录，方便在项目中将grafana以iframe形式嵌入进去

使用grafana admin账号设置一个只有Viewer权限的用户

使用在线base64工具如`https://base64.us`来base以下字符串(注意中间用分号隔开)：`username:password`  

base64后得到： dXNlcm5hbWU6cGFzc3dvcmQ=

在nginx/conf/nginx.conf下面配置

```
include /opt/nginx/conf.d/*.conf;
```

/opt/nginx/conf.d下新建grafana.conf，写入下面内容

```
server {
    listen 1801;
    server_name app-grafana.jalen.com;

    location / {
        proxy_pass http://xx.xxx.xx.xx:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $proxy_host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
        proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header  X-Forwarded-Proto $scheme;
        proxy_set_header  Authorization "Basic dXNlcm5hbWU6cGFzc3dvcmQ=";
        proxy_hide_header 'X-Frame-Options';
        add_header X-Frame-Options ALLOWALL;
    }
}
```

参考： 
http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache_bypass
http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_set_header

# Iframe隐藏左侧边栏，只显示dashboard主体

方法一： 

参考： https://github.com/grafana/grafana/issues/13493#issuecomment-426304112

添加url参数&kiosk=tv

如： https://app-proxy-grafana.jalen.com/d/XumeirY7z/site-admin-features?orgId=1&kiosk=tv

虽然左侧边栏没有了，但是这个时候dashboard配置的variables不显示了，不符合需求

参考issue： 

https://github.com/grafana/grafana/issues/15546

https://github.com/grafana/grafana/issues/20176


方法二：

使用css

参考： https://github.com/grafana/grafana/issues/13493#issuecomment-504309808

<iframe style="margin-left:-30px" id="dashboardIframe" src="http://domain/dashboard?orgId=1&theme=light&from=1553319207005&to=1561095207008&var-domain=All&var-tribe=All&var-squad=All&var-site=All&var-self_service_type=All" class="" width="100%" height="1000px" frameborder="0"></iframe>

```
.iframe-usage{
  position: relative;
  left: -58px;
  width: calc(100% + 58px);
  height: 650px;
  top: -5px;
}
```

方法三：

改grafana的主题css

参考： https://github.com/grafana/grafana/issues/15546#issuecomment-658661495

```bash
[root@chumingcheng grafana]# locate grafana.dark
/usr/share/grafana/public/build/grafana.dark.b5d3c4da30d141a6181f.css
/usr/share/grafana/public/sass/grafana.dark.scss
[root@chumingcheng grafana]# locate grafana.light
/usr/share/grafana/public/build/grafana.light.b5d3c4da30d141a6181f.css
/usr/share/grafana/public/sass/grafana.light.scss
[root@chumingcheng build]# cp grafana.dark.b5d3c4da30d141a6181f.css /users/jalchu/  #备份
[root@chumingcheng build]# vi grafana.dark.b5d3c4da30d141a6181f.css
```

```
.view-mode--tv .submenu-controls{display:none}
.view-mode--tv .submenu-controls{display: flex}
```
```
:.,$ s/str1/str2/g 用字符串 str2 替换正文当前行到末尾所有出现的字符串 str1
:1,$ s/str1/str2/g 用字符串 str2 替换正文中所有出现的字符串 str1

:.,$ s/.view-mode--tv .submenu-controls{display:none}/.view-mode--tv .submenu-controls{display: flex}/g
```

不用重启grafana，改过即时生效





