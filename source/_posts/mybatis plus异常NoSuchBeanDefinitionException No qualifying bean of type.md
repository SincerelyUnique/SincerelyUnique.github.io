---
title: mybatis plus异常NoSuchBeanDefinitionException No qualifying bean of type
date: 2018-05-22 17:20:50
tags:
- mybatis
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

使用mybatis plus时遇到的问题

导致该异常的原因是java目录下的mapper接口所在的包名及路径与mapper配置文件所在路径名字不一致

修改前启动报错：

UserMapper.java路径：com.example.dao

UserMapper.xml路径：com.example.mapper

修改后启动正常：

UserMapper.java路径：com.example.mapper

UserMapper.xml路径：com.example.mapper
