---
title: centos7安装python3.9.0
date: 2020-11-30 17:18:43
tags:
- python
categories:
---

```
centos安装python3.9.0
 
1.先决条件：
sudo yum install gcc openssl-devel bzip2-devel libffi-devel zlib-devel
 
2.下载：
各版本地址：https://www.python.org/ftp/python
wget https://www.python.org/ftp/python/3.9.0/Python-3.9.0.tgz 
 
3.解压：
tar xzf Python-3.9.0.tgz 
 
4.安装：
cd Python-3.9.0 
sudo ./configure --enable-optimizations 
sudo make altinstall 
 
5.创建虚拟环境（for flask project，仅安装python则忽略）
virtualenv -p /usr/local/bin/python3.9 venv
source ./venv/bin/activate
pip install -r requirements.txt
 
 
 
遇到的问题：
1.yum源问题导致gcc安装失败
（1）执行sudo ./configure --enable-optimizations报缺少C编译器
configure: error: in `/opt/python39/Python-3.9.0':
configure: error: no acceptable C compiler found in $PATH
See `config.log' for more details
 
尝试执行：yum install gcc 时报yum库libgomp版本冲突
Error:  Multilib version problems found. This often means that the root
       cause is something else and multilib version checking is just
       pointing out that there is a problem. Eg.:
	... ...
       Protected multilib versions: libgomp-4.8.5-4.el7.i686 != libgomp-4.8.5-36.el7_6.2.x86_64
rpm -qa|grep libgomp: libgomp-4.8.5-36.el7_6.2.x86_64
 
尝试执行yum install gcc --setopt=protected_multilib=false忽略protected multilib，依然报错
Transaction check error:
  package libgomp-4.8.5-36.el7_6.2.x86_64 (which is newer than libgomp-4.8.5-4.el7.i686) is already installed
 
目测是该yum源中libgomp版本还是不符合gcc依赖，只好尝试升级yum源到国内阿里源
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum clean all
yum makecache
vi /etc/yum.repos.d/CentOS-Base.repo  #将所有http改为https，注意备份该文件(vi替换命令 :1,$s/http/https/g)
yum update
yum install gcc
 
 
2.升级gcc版本
centos7使用yum install gcc后默认安装版本是gcc 4.8.5，版本比较低，升级gcc版本：
sudo yum install centos-release-scl
sudo yum install devtoolset-7
scl enable devtoolset-7 bash
gcc --version  # 升级后为7.3.1
 
 
3.pip install -r requirements.txt报错，降低setuptools版本，新版本setuptools移除了Feature
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "/tmp/pip-install-wduw3u48/markupsafe/setup.py", line 6, in <module>
     from setuptools import setup, Extension, Feature
ImportError: cannot import name 'Feature' from 'setuptools'
这个通常会在安装 Flask 项目的依赖时发生，因为 MarkupSafe 是 Flask 的依赖之一
原因是因为 Python 打包工具 setuptools 在 46.0.0 版本删掉了弃用的 Feature，
更新 MarkupSafe 到最新版本
pip install --upgrade pip setuptools==45.2.0
 
 
4.启动项目报警告
UserWarning: Could not import the lzma module. Your installed Python is incomplete. 
Attempting to use lzma compression will result in a RuntimeError
重新编译python：
sudo yum install -y xz-devel
sudo ./configure --enable-optimizations
sudo make altinstall
 
 
5.启动项目报警告
RuntimeWarning: line buffering (buffering=1) isn't supported in binary mode, 
the default buffer size will be used
remember ... it is not a bug, it's a feature
这个是gunicorn 19.x有可能报的问题，可以选择升级gunicorn解决：
pip install gunicorn==20.0.4
 
 
 
参考：
https://tecadmin.net/install-python-3-9-on-centos/  （安装python39）
https://developer.aliyun.com/article/704987  （配置阿里镜像）
https://www.itcoder.tech/posts/how-to-install-gcc-compiler-on-centos-7/ （升级gcc）
https://zhuanlan.zhihu.com/p/127820010  （MarkupSafe报错）
https://stackoverflow.com/questions/57743230/userwarning-could-not-import-the-lzma-module-your-installed-python-is-incomple（lzma问题）
https://askubuntu.com/questions/1216292/python3-runtimewarning-line-buffering-buffering-1-isnt-supported-in-binary-m （gunicorn line buffer问题）
```
