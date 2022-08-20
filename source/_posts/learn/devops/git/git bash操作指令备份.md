---
title: git bash操作指令备份
date: 2021-04-23 11:48:42
tags:
- git
categories:
- [学习, DevOPS, Git]
---

这个在git创建仓库后会有提示，这里仅做备份

```raw
…or create a new repository on the command line
echo "# wechat-miniprogram-demo" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:xxx/wechat-miniprogram-demo.git
git push -u origin main
 
 
 
…or push an existing repository from the command line
git remote add origin git@github.com:xxx/wechat-miniprogram-demo.git
git branch -M main
git push -u origin main
 
 
 
…or import code from another repository
You can initialize this repository with code from a Subversion, Mercurial, or TFS project.
```
