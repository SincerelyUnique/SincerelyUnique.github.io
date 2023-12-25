---
title: node版本切换
date: 2023-12-22 15:03:36
tags:
- vue
categories:
- [学习, 前端知识学习, VueJS]
---

# 需求

项目中需要多个node版本时使用此方法做切换

# 方法

```bash
# 使用n
$ npm install -g n
$ sudo n 16.14.0
$ sudo n ls
$ node -v
$ npm -v
 

# 使用nvm
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
$ source ~/.nvm/nvm.sh
$ nvm --version
$ nvm install 18.18.2
$ nvm use 18.18.2
```
