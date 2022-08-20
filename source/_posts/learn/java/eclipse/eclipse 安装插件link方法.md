---
title: eclipse 安装插件link方法
date: 2015-09-15 16:06:47
tags:
- eclipse
categories:
- [学习, Java语言学习, Eclipse]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

以插件spket为例

1.下载spket插件，解压得到features和plugins。

2.打开eclipse根目录，创建两个文件夹：links（放配置文件）和 MyPlugins（放插件）

1.

/home/jalen_chu/Jalen_Chu/SDK/eclipse/links

此文件夹里创建文件spket.link,

注意后缀名为.link,

打开编辑（记事本）path：      path=MyPlugins/spket

2.

/home/jalen_chu/Jalen_Chu/SDK/eclipse/MyPlugins

此文件夹放插件，将解压好的插件features和plugins复制过来注意路径

home/jalen_chu/Jalen_Chu/SDK/eclipse/MyPlugins/spket/eclipse/features

home/jalen_chu/Jalen_Chu/SDK/eclipse/MyPlugins/spket/eclipse/plugins

3.完成，打开eclipse查看preference

