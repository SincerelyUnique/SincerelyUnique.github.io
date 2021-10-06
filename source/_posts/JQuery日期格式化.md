---
title: JQuery日期格式化
date: 2019-01-25 13:36:03
tags:
- jquery
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```javascript
function setDateValueDefault() {
    var fromDay = new Date();
    fromDay.setDate(fromDay.getDate() - 30);
    var endDay = new Date();
    var fromDayStr = fromDay.Format("yyyy-MM-dd hh:mm:ss");
    var toDayStr = endDay.Format("yyyy-MM-dd hh:mm:ss");
    $("#dateFrom").val(fromDayStr);
    $("#dateTo").val(toDayStr);
}
 
Date.prototype.Format = function (fmt) {
    var o = {
        "M+": this.getMonth() + 1,
        "d+": this.getDate(),
        "h+": this.getHours(),
        "m+": this.getMinutes(),
        "s+": this.getSeconds(),
        "q+": Math.floor((this.getMonth() + 3) / 3),
        "S": this.getMilliseconds()
    };
    if (/(y+)/.test(fmt))
        fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
    for (var k in o)
        if (new RegExp("(" + k + ")").test(fmt))
            fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
    return fmt;
};
```
