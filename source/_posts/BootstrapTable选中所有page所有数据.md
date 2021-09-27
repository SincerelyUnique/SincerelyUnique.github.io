---
title: BootstrapTable选中所有page所有数据
date: 2020-04-27 10:24:28
tags:
- bootstrap
categories:
---

```javascript
  var $table = $('#table')
 
  $(function() {
    $('#checkAll').click(function () {
      $table.bootstrapTable('togglePagination').bootstrapTable('checkAll').bootstrapTable('togglePagination')
    })
 
    $('#uncheckAll').click(function () {
      $table.bootstrapTable('togglePagination').bootstrapTable('uncheckAll').bootstrapTable('togglePagination')
    })
  })
```

参考：

https://examples.bootstrap-table.com/#issues/1167.html

https://examples.bootstrap-table.com/#issues/1167.html#view-source
