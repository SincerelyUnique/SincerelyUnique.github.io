---
title: ubuntu javac(java -version)已包含在下列文件中
date: 2015-09-08 10:02:01
tags:
- ubuntu
categories:
- [学习, DevOPS, Ubuntu]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

问题：

```
jalen_chu@jalen:~$ javac
程序 'javac' 已包含在下列软件包中：
* default-jdk
* ecj
* gcj-4.8-jdk
* openjdk-7-jdk
* gcj-4.6-jdk
* openjdk-6-jdk
  请尝试：sudo apt-get install <选定的软件包>
```
（java -version同上）


方案：


输入：$ sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.7.0_75/bin/javac 300

返回：update-alternatives: using /usr/lib/jvm/jdk1.7.0_75/bin/javac to provide /usr/bin/javac (javac) in 自动模式

javac

...

输入：$ sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.7.0_75/bin/java 300

返回：update-alternatives: using /usr/lib/jvm/jdk1.7.0_75/bin/java to provide /usr/bin/java (java) in 自动模式

java -version


