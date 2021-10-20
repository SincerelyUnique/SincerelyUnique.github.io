---
title: ubuntu语言支持不见了？
date: 2015-10-27 11:36:21
tags:
- ubuntu
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

请终端执行下列语句：

$ sudo apt-get install language-selector-gnome

说明：ubuntu安装中文输入法时，尽量不要安装 im-switch （新版本已被im-config替代），安装im-switch 会导致内置的im-config和languagesupport被卸载，所以解决方法就是先卸载掉 im-switch ，重新装回 im-config和language support。

