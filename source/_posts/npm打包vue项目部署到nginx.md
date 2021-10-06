---
title: npm打包vue项目部署到nginx
date: 2019-01-17 14:32:15
tags:
- npm
- vue
- nginx
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. Build vue project，生成dist目录:

> npm run build

2. 下载nginx，解压，测试nginx，修改nginx.conf端口号为8082，启动nginx

> start .\nginx.exe

3. 访问localhost:8082显示nginx主页

4. nginx测试完毕，此时将我们项目构建后的dist目录整个复制到nginx根目录的html文件夹下

> C:\Users\jalchu\Nginx\nginx-1.15.8\html\dist

5. 修改nginx的conf目录下文件nginx.conf，编辑server节点

```
        listen       8082;
        server_name  localhost;
 
        location / {
            root   html/dist;
            index  index.html index.htm;
            try_files $uri $uri/ @router;
        }
		
        location @router {
            rewrite ^.*$ /index.html last;
        }
```

4. Reload config配置

> .\nginx.exe -s reload

5. 访问localhost:8082即可

参考：

https://my.oschina.net/u/1760791/blog/1575808
