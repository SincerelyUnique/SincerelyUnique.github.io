---
title: vue mounted内调用方法报is not define的一种情况记录
date: 2021-01-09 22:09:24
tags:
- vue
categories:
---

页面初始化时，子组件mounted方法调用方法A走ajax拿数据，拿完后渲染bootstrap-table，

bootstrap-table有一列绑定自定义function，该function内自定义dom，dom元素中有onchange方法

当bootstrap-table渲染完毕后，该列为selectbox，但是点击切换selectbox方法时报

```raw
configdisable:1 Uncaught ReferenceError: storeChangeRc is not defined
    at HTMLSelectElement.onchange (configdisable:1)
```

子组件代码如下 

```javascript
    mounted() {
      this.getTableData()
    },
    methods: {
      getTableData() {
        let fromDate = this.timeRange[0]
        let toDate = this.timeRange[1]
        let tableNode = $('#configTable')
        tableNode.bootstrapTable('resetView')
        tableNode.bootstrapTable('destroy')
        tableNode.bootstrapTable({
          url: '/api/xxxx',
          method: 'POST',
          contentType: 'application/x-www-form-urlencoded',
          queryParamsType: '',
          formatLoadingMessage: function() {
            return '<b style="font-size: 12px;">Loading, please wait</b>';
          },
          queryParams: function queryParams(params) {
            $("#configTable").find("input").attr('placeholder', "Search");
            let requestNo = $("#configTable").find('.search').find('input').val();
            return {
              pageNumber: params.pageNumber,
              pageSize: params.pageSize,
              dateFrom: fromDate,
              dateTo: toDate,
              searchText: requestNo
            }
          },
          toolbar: '#toolbar',
          cache: false,
          striped: true,
          sidePagination: 'server',
          pagination: true,
          pageList: [10, 20, 50],
          pageSize: 10,
          pageNumber: 1,
          clickToSelect: true,
          search: true,
          showRefresh: true,
          showColumns: true,
          fixedColumns: true,
          fixedNumber: 1,
          undefinedText: '',
          columns: [
            {
              checkbox: true
            },
            {
              field: 'flag',
              title: 'Flag',
              visible: false
            }, {
              field: 'tablename',
              title: 'TableName',
              visible: false
            }, {
              field: 'fieldname',
              title: 'FieldName',
              visible: false
            }, 
            {
              field: 'issue',
              title: 'Change RC',
              formatter: this.addSelect
            },
            {
              field: 'createtime',
              title: 'CreateTime',
              visible: false
            }, {
              field: 'lastmodifiedtime',
              title: 'LastModifiedTime',
              visible: false
            }
          ]
        })
      },
 
      storeChangeRc(report_id) {
        ... ...
      },
 
      addSelect(value, row) {
        let selectDom = "<select class=\"form-control\" id=\"" + "rc_" + row.id + "\"" +
          "onchange=\"storeChangeRc(" + "'" + row.id + "'" + ")\" title=\"\">" +
          "<option value=\"\" selected=\"selected\" style=\"display: none\"></option>";
        ... ...
        ... ...
        selectDom += "</select>";
        return selectDom;
      },
```

解决，在mounted方法内添加window.storeChangeRc = this.storeChangeRc

```javascript
    mounted() {
      this.getTableData()
      window.storeChangeRc = this.storeChangeRc
    },
```
