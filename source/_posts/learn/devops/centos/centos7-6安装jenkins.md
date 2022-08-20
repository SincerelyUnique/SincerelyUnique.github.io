---
title: centos7.6安装jenkins
date: 2021-08-02 11:49:43
tags:
- jenkins
- linux
categories:
- [学习, DevOPS, CentOS]
---

# centos7.6安装jenkins

主要有：jenkins（war包），jdk8，tomcat9，git，maven3.8

主要操作：安装这些依赖环境后启动tomcat一路配置就好了

```raw
下载tomcat9
地址：https://tomcat.apache.org/download-90.cgi
 
 
下载jenkins war包放到tomcat的webapps目录
 
[root@node tomcat9]# ./bin/startup.sh   # 启动tomcat
访问：http://192.168.2.174:8080/jenkins
 
Unlock Jenkins，把下面内容复制进去
[root@node tomcat9]# cat /root/.jenkins/secrets/initialAdminPassword
选择自定义安装插件，安装下面几个插件：ssh、publish over ssh、git parameter、git、maven itegration
安装插件后会提示设置一下用户名密码和邮箱之类的，随便设置一下comfirm就好
提示jenkins url：http://192.168.2.174:8080/jenkins/
进入http://192.168.2.174:8080/jenkins/pluginManager/available
搜索maven integration并安装
 
 
安装git（yum install -y git）
 
 
安装maven
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_301
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=.:${JAVA_HOME}/bin:$PATH
export M2_HOME=/usr/local/maven/maven3
export CLASSPATH=$CLASSPATH:$M2_HOME/lib
export PATH=$PATH:$M2_HOME/bin
maven配置阿里云镜像mirror，mirrorOf改成*
 
 
 
在jenkins 全局配置中配置jkd、git、maven, 就是上面配置的环境变量的路径
http://192.168.2.174:8080/jenkins/configureTools/
```
