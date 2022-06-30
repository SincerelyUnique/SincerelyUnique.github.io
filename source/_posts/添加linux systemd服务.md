---
title: 添加linux systemd服务
date: 2022-06-01 14:34:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> 本文介绍两种执行方式：一种是通过systemd执行二进制文件，一种是执行shell脚本，在实际使用中可以根据自己需要选择恰当方式。

# 方式一：可执行二进制文件

> 我们通过pyinstaller将单个python脚本文件转化为二进制可执行文件，然后再添加到systemd服务

pyinstaller可以将脚本文件转为可执行文件，比如在windows环境下，可以将脚本转为.exe格式的文件，在linux环境下，可以转为一个可执行的二进制文件，因为python比较简单，我们以python为例。

## 创建虚拟环境

创建虚拟环境并通过虚拟环境执行python相关操作，可以和外部python环境隔离开，降低耦合，消除对系统环境的依赖，避免错误。

```bash
[root@localhost jalen]# pwd
[root@localhost jalen]# mkdir test
[root@localhost jalen]# cd test/
[root@localhost test]# whereis python
[root@localhost test]# virtualenv -p /usr/bin/python3.6 venv
[root@localhost test]# source venv/bin/activate
```

## 生成可执行文件

虚拟环境搭好后，进入虚拟环境。创建简单python文件，安装pyinstaller依赖。

```bash
(venv) [root@localhost test]# python -V
(venv) [root@localhost test]# pip list
(venv) [root@localhost test]# pip install pyinstaller
(venv) [root@localhost test]# touch server.py
(venv) [root@localhost test]# vi server.py    # print("Hello World!") 随便打印一些
(venv) [root@localhost test]# python server.py

(venv) [root@localhost test]# pyinstaller server.py  # 会生成可执行文件，在dist目录下
(venv) [root@localhost test]# ./dist/server/server  # 测试生成的可执行文件
```

## 创建并启动systemd服务

创建.service服务配置文件，配置服务相关信息，并通过systemctl启动该服务

```bash
(venv) [root@localhost test]# touch echo-server.service
(venv) [root@localhost test]# vi echo-server.service
(venv) [root@localhost test]# cp echo-server.service /etc/systemd/system
(venv) [root@localhost test]# ls /etc/systemd/system | grep echo-server
(venv) [root@localhost test]# systemctl daemon-reload
(venv) [root@localhost test]# systemctl enable echo-server.service
(venv) [root@localhost test]# systemctl start echo-server.service  
(venv) [root@localhost test]# systemctl status echo-server.service
```

```file
[Unit]
Description=Service that keeps running the echo-server from startup.
After=network.target

[Install]
WantedBy=multi-user.target

[Service]
Type=simple
ExecStart=/home/jalen/test/dist/server/server   # 这里指向可执行二进制文件，或者是shell脚本
WorkingDirectory=/home/jalen/test/dist/server   # optional
Restart=always
RestartSec=5
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=%n
```

## 查看服务启动后运行日志

这里提供两种方式查看日志，一种是通过journalctl，一种是tail log。

```bash
(venv) [root@localhost test]# journalctl -f -u echo-server.service  # 查看运行日志
(venv) [root@localhost log]# tail -f /var/log/messages  # 有可能会发现报权限错误，可能是由于SELinux安全控制，将可执行文件copy到/usr/local/bin下再尝试
```

## 关闭服务

退出虚拟环境，通过命令关闭服务。

```bash
(venv) [root@localhost test]# deactivate
[root@localhost test]# systemctl stop echo-server.service
[root@localhost test]# journalctl -f -u echo-server.service
[root@localhost test]# sudo rm /etc/systemd/system/echo-server.service
[root@localhost test]# systemctl daemon-reload
[root@localhost test]# systemctl reset-failed
```

# 方式二：执行Shell脚本

大部分流程和上面一致，只是在systemd .service文件里将二进制可执行文件替换为shell脚本的路径。

## 创建shell脚本

```bash
[root@localhost test]# touch server.sh
[root@localhost test]# vi server.sh 
```

```shell
#!/bin/bash
SCRIPT_PATH=/home/jalen/test/server.py
PYTHON_PATH=/home/jalen/test/venv/bin/python
$PYTHON_PATH $SCRIPT_PATH
exit 1
```

```bash
[root@localhost test]# chmod +x server.sh
[root@localhost test]# cp server.sh /usr/sbin/  # shell脚本移动到指定路径
[root@localhost test]# vi echo-server.service   # ExecStart=/usr/sbin/server.sh  # 只需改这行， 后续操作和上面一样
```

参考：
- https://abhinand05.medium.com/run-any-executable-as-systemd-service-in-linux-21298674f66f  （important）
- https://serverfault.com/questions/957084/failed-at-step-exec-spawning-permission-denied
- https://pyinstaller.org/en/stable/#quickstart
