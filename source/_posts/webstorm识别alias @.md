---
title: webstorm识别alias @
date: 2020-02-19 14:00:00
tags:
- ide
categories:
---

根目录新建alias.config.js，在webstorm配置里引入

```javascript
/**
 * 由于 Vue CLI 3 不再使用传统的 webpack 配置文件，故 WebStorm 无法识别别名
 * 本文件对项目无任何作用，仅作为 WebStorm 识别别名用
 * 进入 WebStorm preferences -> Language & Framework -> JavaScript -> Webpack，选择这个文件即可
 * */
const resolve = dir => require('path').join(__dirname, dir);
 
module.exports = {
    resolve: {
        alias: {
            '@': resolve('src')
        }
    }
};
```
