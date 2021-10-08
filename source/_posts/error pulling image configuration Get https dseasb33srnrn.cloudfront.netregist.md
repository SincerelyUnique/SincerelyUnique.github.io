---
title: error pulling image configuration Get https dseasb33srnrn.cloudfront.netregist
date: 2018-04-18 15:17:58
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

从[DaoCloud](https://www.daocloud.io/)获取镜像

```bash
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://df1e2ed4.m.daocloud.io
```

将其加入daemon.json文件中

```bash
root@jalen demo-eureka-server]# vi /etc/docker/daemon.json
```

修改后

```bash
{
    "registry-mirrors": [
        "http://df1e2ed4.m.daocloud.io"
    ]
}
```
