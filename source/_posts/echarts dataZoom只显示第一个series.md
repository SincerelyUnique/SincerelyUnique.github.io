---
title: echarts dataZoom只显示第一个series
date: 2024-01-02 14:34:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

详细参考git issue： https://github.com/apache/echarts/issues/19255#event-10840503924

```js
option = {
  title: {
    text: 'Stacked Line'
  },
  tooltip: {
    trigger: 'axis'
  },
  legend: {
    data: ['Email', 'Union Ads']
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '10%',
    containLabel: true
  },
  toolbox: {
    feature: {
      saveAsImage: {}
    }
  },
  xAxis: {
    type: 'category',
    boundaryGap: false,
    data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
  },
  yAxis: {
    type: 'value'
  },
  dataZoom: [
    {
      type: 'slider',
      showDataShadow:true,
    },
    {
      type: 'inside'
    }
  ],
  series: [
    {
      name: 'Email',
      type: 'bar',
      stack: 'Total',
      data: [120, 132, 101, 134, 90, 230, 210]
    },
    {
      name: 'Union Ads',
      type: 'bar',
      stack: 'Total',
      data: [220, 182, 191, 134, 290, 230, 150]
    },
    
  ]
};
```
