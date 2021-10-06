---
title: es6的promise简单使用（ajax）
date: 2019-05-05 09:25:09
tags:
- es6
- ajax
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. common method

```javascript
function getAjaxPromise (url, params){
    return new Promise(function(resolve, reject){
        $.ajax({
            url: url,
            type: 'post',
            async: true,
            contentType: "application/json",
            data: JSON.stringify(params),
            success: function(data){
                resolve(data);
            },
            error: function(error){
                reject(error);
            }
        });
    });
}
```

2. call common method

```javascript
function callCommonPromiseMethod() {
    var params = {};
    var p1 = getAjaxPromise('/report/xx/data', params);
    p1.then(function (data1) {
        return getAjaxPromise('/report/xxx/data', data1);
    }).then(function (data2) {
        return getAjaxPromise('/report/xxxx/data', data2);
    }).then(function(data3){
        
    });
}
```
