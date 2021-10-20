---
title: linux配置.bashrc环境变量
date: 2016-09-20 10:17:05
tags:
- linux
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
export M2_HOME=/home/jalenchu/jalen_chu/Tools/maven/apache-maven-3.0.4

export JAVA_HOME=/home/jalenchu/jalen_chu/Tools/jdk/jdk1.7.0_75/bin

export JRE_HOME=/home/jalenchu/jalen_chu/Tools/jdk/jdk1.7.0_75/jre/bin

export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH  

export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$M2_HOME/bin:$JAVA_HOME:$PATH

export http_proxy=http://jalen_chu:mar%402016@172.36.100.238:65000

export https_proxy=https://jalen_chu:mar%402016@172.36.100.238:65000

export ftp_proxy=ftp://jalen_chu:mar%402016@172.36.100.238:65000

```
