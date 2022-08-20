---
title: Pycharm远程调试配置
date: 2022-01-07 14:34:43
tags: pycharm
categories:
- [学习, Python语言学习, Python]
---

> 主要解决问题： 本地访问国外DB速度慢，这里将code也同步到国外服务器上，启用远程调试

# 远程server安装ngrok，测试bot
```bash
[jalchu@centos7 ~]$ wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.tgz --no-check-certificate  #下载
[jalchu@centos7 ~]$ tar -zxvf ngrok-stable-linux-amd64.tgz  #解压
[jalchu@centos7 ~]$ rm ngrok-stable-linux-amd64.tgz   #删除压缩包，备份也可以
[jalchu@centos7 ~]$ ./ngrok http -region us 8080   #启动测试
[jalchu@centos7 ~]$ ./ngrok authtoken 1cWAwYiXZqd3AL4JGeght0JZhN2_3dxAd6UXVxxxxx  #官网登录后获取的token，无token只能使用1个小时，1小时后需要重连
```


# 本地pycharm配置sftp，自动同步本地修改文件
1. 路径Tools -> Deployment -> Configuration -> Add(+ SFTP)
2. 配置SSH configuration
3. 点击Autodetect自动搜索当前sftp账号可访问的根路径，一般是/users/jalchu （jalchu是我的account）
4. Web Server Url不配置有警告，可以随意写一个正确的url，目前用不上
5. Mappings里配置本地文件夹到远程server文件夹的映射，可以在指定一个Deployment Path，server上可以没有该文件夹，我们第一次上传时会自动创建
6. Excluded Paths可以设置一些忽略的文件夹，这些文件夹内的内容在更改后不会自动同步到远程server
7. 开启本地修改自动上传到远程server： Tools -> Deployment -> Automatic Upload, 选择刚刚配置的server即可
8. 右键项目根目录： Deployment - Upload to xxx  (这需要一定时间，具体取决项目大小)



# 远程server创建虚拟环境，本地连接远程虚拟环境
```bash
[jalchu@centos7 ~]$ whereis python
[root@centos7 jalchu]# virtualenv -p /usr/bin/python3.6 venv
[root@centos7 jalchu]# source venv/bin/activate
(venv) [root@centos7 jalchu]# pip install setuptools==45.3.0  #fix ImportError: cannot import name 'Feature'
(venv) [root@centos7 jalchu]# pip install Flask==1.1.2    #fix ERROR: which is incompatible.
(venv) [root@centos7 jalchu]# pip install Jinja2==2.11.2
(venv) [root@centos7 jalchu]# pip install -r provision/requirements.txt
[root@centos7 jalchu]# chown -R jalchu:jalchu venv/
```
1. File -> Settings -> Python Interpreter -> Add -> SSH Interpreter -> Existing Server configuration
2. 选择上面创建好的虚拟环境里的python可执行文件即可
