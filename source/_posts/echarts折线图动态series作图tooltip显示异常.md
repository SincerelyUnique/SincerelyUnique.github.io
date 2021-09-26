---
title: echarts折线图动态series作图tooltip显示异常
date: 2021-06-16 15:16:43
tags:
- echarts
categories:
---

# echarts折线图动态series作图tooltip显示异常

我这边遇到的是echarts折线图动态添加series的时候，发现tooltip显示不对，有好多undefined出现。

这块确实是一个前端组件在使用上的问题，前端使用了echarts做图，而echarts有个比较奇怪（可能也是比较正常的）的地方就是一旦这个div 实例化后，后续再次init实例化这个div其实可能是一个假的实例化操作，也就是后续的渲染可能是无效的， 正确的做法是需要先销毁前一个实例化的实例后才会正确的显示后面的实例，否则前一个实例化的图像会串改后续的图，执行下述代码即可做到销毁实例。

```javascript
echarts.init(document.getElementById('div')).dispose(); // 销毁实例
var myChart1 = echarts.init(document.getElementById('div')); // 再次创建实例
```

参考：https://blog.csdn.net/woyaojinali/article/details/109623453
