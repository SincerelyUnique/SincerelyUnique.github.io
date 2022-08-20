---
title: bootstrapTable清空search框内容并刷新页面
date: 2020-06-30 11:37:04
tags:
- bootstrap
- javascript
categories:
- [学习, 前端知识学习, Bootstrap]
---

```javascript
let tableNode = $("#tableSelector");
tableNode.bootstrapTable("resetSearch","");
tableNode.bootstrapTable('refresh');
```

参考：

https://github.com/wenzhixin/bootstrap-table/issues/1230

https://stackoverflow.com/questions/53177296/clear-filter-values-in-bootstraptable
