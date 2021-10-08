---
title: virtualbox挂载共享文件夹
date: 2018-10-08 10:27:29
tags:
- virtualbox
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

![vb01](../images/vb01.png)

挂载目录是provision目录，这个是我的项目根目录

把它挂载到mnt目录下，在mnt目录下创建一个provision文件夹

执行

```bash
sudo mount -t vboxsf provision /mnt/provision/
```

使用pycharm导入mnt下挂载的项目即可
