---
title: 创建vue cli项目
date: 2020-06-08 15:03:36
tags:
- vue
categories:
---

```
1. install nodejs(javascript run env)
   install: https://nodejs.org/zh-cn/download/
   set path to system env variable
   test: node -v
2. install npm(nodejs package manager)
   install: ignore (contains in new version nodejs)
   test: npm -v
3. install webpack(open source front-end packaging tool)
   execute: npm install webpack -g
   test: webpack -v
   install path(default): C:\Users\<username>\AppData\Roaming\npm
4. install vue-cli
   execute: npm install --global vue-cli
   test: vue -V
   install path(default): C:\Users\<username>\AppData\Roaming\npm
5. create vue cli project in webstorm
   File -> New -> Project -> Vue.js
   Select Node interpreter and vue-cli location
   Next -> Next -> Next ...
6. run vue cli project by npm
   execute: npm run dev
7. install package
   execute: npm install <typescript>
7. packaging
   npm run build
```
