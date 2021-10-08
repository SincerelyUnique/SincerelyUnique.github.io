---
title: centos7防火墙开放端口
date: 2018-06-21 09:08:11
tags:
- firewall
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```bash
cat /etc/redhat-release             # 查看CentOS版本



firewall-cmd --state                # 查看防火墙状态
firewall-cmd --permanent --zone=public --add-port=8080/tcp                   # 永久的添加该端口
firewall-cmd --reload                     # 加载配置，使得修改有效。
firewall-cmd --permanent --zone=public --list-ports                # 查看开启的端口



systemctl stop firewalld.service    #关闭防火墙
systemctl status firewalld       # 查看防火墙状态
```
