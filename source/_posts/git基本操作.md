---
title: git基本操作
date: 2017-08-07 21:03:15
tags:
- git
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

场景：操作github时，例如提交本地项目或文件等。

linux系统可以使用sudo直接安装git命令，而windows系统需要下载官方git安装包，然后使用git bash操作，他们操作命令基本一致

```
touch README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/BrentHuang/MyRepo.git
git push -u origin master
```

- 在本地新建一个分支： git branch Branch1
- 切换到你的新分支: git checkout Branch1
- 将新分支发布在github上： git push origin Branch1
- 在本地删除一个分支： git branch -d Branch1
- 在github远程端删除一个分支： git push origin :Branch1   (分支名前的冒号代表删除)
