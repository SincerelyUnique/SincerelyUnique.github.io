---
title: windows安装dig
date: 2020-06-28 14:41:30
tags:
- dig
- dns
categories:
---

```
dig是一个Linux下用来查询DNS信息的工具，全称是Domain Information Groper，
与nslookup类似，但比nslookup功能更强大。
Windows下只有nslookup，如果也想用到dig命令，需要自己动手安装。
dig作为bind的一部分，我们需要去bind9官网的镜像中下载安装包，
bind是现在使用最为广泛的DNS服务器软件，最早由伯克利大学的一名学生编写，
最新的版本是9.9.9(截至今天)，目前由ISC（Internet Systems Consortium）编写和维护。
 
1. 下载：访问https://www.bind9.net/download，随便选择一个mirrors link下载
  （有些可能打不开，多试几个，我访问的是United States的mirror ftp://ftp.isc.org/isc/bind9/）
2. 选择最新版本9.9.9/版本进入，点击BIND9.9.9.x64.zip下载
3. 解压zip文件，点击vcredist_x64.exe安装Microsoft Visual C++ 2012 Redistribution
4. 将解压后的dll文件（9.9.9版本有9个dll文件）复制到C:\Windows\System32目录下
5. 将解压后的dig.exe也复制到C:\Windows\System32目录下
6. 安装完毕，cmd打开windows终端，测试： dig baidu.com
 
 
 
 
使用python dig SRV测试，SRV参考（https://en.wikipedia.org/wiki/SRV_record）
def test_dig_in_windows():
    """
    如果不在windows上安装dig，使用subprocess执行下面命令会报error：
        WindowsError: [Error 2] The system cannot find the file specified
    :return: srv
    """
    import subprocess
    import shlex
 
    cmd = 'dig @ns1.xxx.net _sips._tcp.xxx.xxx. -t SRV +short'
    process = subprocess.Popen(shlex.split(cmd), stdout=subprocess.PIPE)
    out, err = process.communicate()
    print(out)
```
