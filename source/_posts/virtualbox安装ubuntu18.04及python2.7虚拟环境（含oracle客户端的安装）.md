---
title: virtualbox安装ubuntu18.04及python2.7虚拟环境（含oracle客户端的安装）
date: 2018-09-30 13:58:24
tags:
- virtualbox
- ubuntu
- python
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
1. 下载ubuntu18系统iso镜像，使用virtual box安装该镜像
 
2. 安装vitrul box增强功能，主要用来与windows系统共享文件夹
   参考：https://blog.csdn.net/sinat_32582203/article/details/71633678
 
3. 设置双向拷贝
   参考：https://blog.csdn.net/baidu_37503452/article/details/78707806
 
4. 共享文件夹后因为不是用root用户登录的，vboxsf用户组不存在，需要从终端添加当前用户进用户组，如：sudo adduser jalen vboxsf,添加完毕重启生效
   参考：https://blog.csdn.net/boarmy/article/details/19074473
5. 注意不要使用sudo chmod -R 777 /usr，不要为user赋予最高权限
 
6. 安装jdk并配置环境变量（可选，因为ubunut自带openjdk）
	export JAVA_HOME=/home/jalen/Jalen/tools/jdk/jdk-8u181-linux-x64/jdk1.8.0_181
	export JRE_HOME=/home/jalen/Jalen/tools/jdk/jdk-8u181-linux-x64/jdk1.8.0_181/jre
	export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
	export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$JAVA_HOME:$PATH
 
7. 安装pycharm
   参考：https://blog.csdn.net/xudailong_blog/article/details/78797559 （ubuntu设置pycharm桌面快捷启动方式）
 
8. 安装python 2.7.15，官网下载源码包（.tgz），加压后安装，先装gcc再install，install需要sudo最高权限
   参考：https://blog.csdn.net/qq_36235192/article/details/81456626
         https://blog.csdn.net/sinat_33186733/article/details/51762499
 
9. 配置oracle环境，安装pip（python2 pip），安装zlib（不要用yum，ubuntu用apt-get install安装）
   参考：https://cx-oracle.readthedocs.io/en/latest/user_guide/installation.html#installing-cx-oracle-on-linux （Installing cx_Oracle on Linux章节）
   参考：https://blog.csdn.net/nsrainbow/article/details/78447492 （no module pip问题）
   参考：https://www.cnblogs.com/MnCu8261/articles/5835604.html （安装zlib，要在之前download的python包中去掉一个注释，重新构建python环境）
   参考：https://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html （oracle linux客户端zip包下载）
 
   ubuntu安装的是libaio1
 
10. 配置虚拟环境
   创建虚拟文件夹：virtualenv -p /usr/local/bin/python venv
   进入虚拟环境： source ./venv/bin/activate
   安装依赖：pip install -r requirements.txt
   启动项目：./run.sh &
```
