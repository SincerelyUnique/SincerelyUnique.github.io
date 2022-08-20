---
title: ubuntu java环境变量配置
date: 2015-09-10 11:06:49
tags:
- ubuntu
categories:
- [学习, DevOPS, Ubuntu]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

编辑：$ sudo gedit /etc/profile

文件结尾加：

```
export JAVA_HOME=/usr/lib/jvm/jdk1.7.0_75
export JRE_HOME=/usr/lib/jvm/jdk1.7.0_75/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH  
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$JAVA_HOME:$PATH
```

结束：

$ javac

...

$ java -version

