---
title: JS正则校验MAC地址
date: 2018-05-03 08:31:01
tags:
- js
categories:
- [学习, 前端知识学习, JavaScript]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

JS正则校验MAC地址，以破折号分割，正则不太好，这里记录一下

```javascript
function checkMacAddress( macAddress ) {
    var regex = "([A-Fa-f0-9]{2}-){5}[A-Fa-f0-9]{2}";
    //var regex = "(([A-Fa-f0-9]{2}-){5}[A-Fa-f0-9]{2})|(([A-Fa-f0-9]{2}:){5}[A-Fa-f0-9]{2})"; // 含冒号
    var regexp = new RegExp(regex);
    if (!regexp.test(macAddress)) {
        console.log("Mac地址格式不正确，请检查")
        return false;
    }
    return true;
}
```

附：JS替换字符串中所有符合条件的字符，如替换所有的冒号（全角，半角）为破折号，并转小写

```javascript
            var regSingleByte = new RegExp(":","g");    // 半角冒号匹配全部
            var regDoubleByte = new RegExp("：","g");    // 全角冒号匹配全部
            var mac = macDom.val().toLowerCase().replace(regSingleByte,"-").replace(regDoubleByte,"-");
            macDom.val(mac);
```
