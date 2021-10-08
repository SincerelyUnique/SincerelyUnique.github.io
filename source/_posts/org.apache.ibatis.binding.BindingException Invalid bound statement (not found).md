---
title: org.apache.ibatis.binding.BindingException Invalid bound statement (not found)
date: 2018-06-19 15:04:04
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

三种情况：

1. mapper的namespace写的不对！！！注意系修改。

2. UserDao的方法在UserDao.xml中没有，然后执行UserDao的方法会报此

3. UserDao的方法返回值是List,而select元素没有正确配置ResultMap,或者只配置ResultType!

4. 如果你确认没有以上问题,请任意修改下对应的xml文件,比如删除一个空行,保存.问题解决...

我的属于第四种，随便改一下xml，重新编译
