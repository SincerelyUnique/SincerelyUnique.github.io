---
title: bootstrap-table服务端分页及导出
date: 2019-03-19 12:43:32
tags:
- bootstrap-table
categories:
- [学习, 前端知识学习, Bootstrap]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bootstrap-Table Export</title>
    {#    bootstrap和jquery自行引入#}
    <link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-table/1.13.5/bootstrap-table.css" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-table/1.13.5/bootstrap-table.js"></script>
    <script src="https://rawgit.com/hhurz/tableExport.jquery.plugin/master/tableExport.js"></script>
    <script src="https://rawgit.com/hhurz/tableExport.jquery.plugin/master/libs/FileSaver/FileSaver.min.js"></script>
    <script src="https://rawgit.com/hhurz/tableExport.jquery.plugin/master/libs/js-xlsx/xlsx.core.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-table/1.13.5/extensions/export/bootstrap-table-export.js"></script>
</head>
<body>
    <div>
        <table id="bootstrap-table" style="width: 100%;" class="table"></table>
    </div>
</body>
</html>
```

2. js

```javascript
function showTable() {
    let table = $("#bootstrap-table");
    table.bootstrapTable('resetView');
    table.bootstrapTable('destroy');
    table.bootstrapTable({
        cache: false,
        striped: true,
        url: '表格数据源',
        method: 'post',
        contentType: "application/x-www-form-urlencoded",
        queryParamsType:'',
        queryParams: function queryParams(params) {
            //表格组件右上角search框内的查询参数
            let param6 = $("#bootstrap-table").find(".search").find("input").val();
 
            //更新Export链接地址
            changeExportUrlHref(param6);
 
            //组建传到后台的查询参数
            return {
                //用于后端做oracle分页查询
                pageNumber: params.pageNumber,
                pageSize: params.pageSize,
                //自定义查询参数
                param1: $("#param1").val(),
                param2: $("#param2").val(),
                param3: $("#param3").val(),
                param4: $("#param4").val(),
                param5: $("#param5").val(),
                param6: param6,
            };
        },
        sidePagination: 'server',
        pagination: true,
        pageList: [10, 20, 50],
        pageSize: 10,
        pageNumber: 1,
        showToggle: true,
        cardView: false,
        detailView: false,
        showExport: true,
        exportDataType: "selected",  //不是select的话会触发两次导出
        exportTypes: ['excel'],
        Icons:'glyphicon-export',
        exportOptions: {
            fileName: 'file name',
            worksheetName: 'sheet name'
        },
        showRefresh:true,
        search: true,
        columns: [
            {
                field: '1',
                title: '1',
                sortable: true
            }, {
                field: '2',
                title: '2',
                sortable: true
            }, {
                field: '3',
                title: '3',
                sortable: true
            }, {
                field: '4',
                title: '4',
                sortable: true
            }, {
                field: '5',
                title: '5',
                sortable: true
            }, {
                field: '6',
                title: '6',
                sortable: true
            }, {
                field: '7',
                title: '7',
                sortable: true
            },
        ]
    });
}
 
/**
 * 更新导出按钮的url
 * @param param6 表格组件右上角search框内输入的查询参数
 */
function changeExportUrlHref(param6) {
    //构建导出URL
    let url = window.location.protocol + '//' + window.location.host + '/export';
    url = url + '?param1=' + $("#param1").val();
    url = url + '&param2=' + $("#param2").val();
    url = url + '&param3=' + $("#param3").val();
    url = url + '&param4=' + $("#param4").val();
    //因为是get请求，下面两个参数有可能被编码，所以encode一下
    url = url + '&param5=' + encodeURIComponent(encodeURIComponent($("#param5").val()));
    url = url + '&param6=' + encodeURIComponent(encodeURIComponent($("#param6").val()));
    //替换组件下链接地址
    $("#bootstrap-table ").find("a").attr('href', url);
}
```

3. python export

```python
@report.route('/export', methods=['GET'])
@login_required
def export():
    import urlparse
    import pandas as pd
    from io import BytesIO
    param1 = request.args.get('param1')
    param2 = request.args.get('param2')
    param3 = request.args.get('param3')
    param4 = request.args.get('param4')
    param5 = request.args.get('param5')
    param5 = urlparse.unquote(param5) if param5 else None
    param6 = request.args.get('fromDate')
    param6 = urlparse.unquote(param6) if param6 else None
 
    output = BytesIO()
    writer = pd.ExcelWriter(output)
    workbook = writer.book
    header_fmt = workbook.add_format({
        'font_size': 10,
        'bold': True,
        'fg_color': '#D7E4BC',
        'border': 1})
 
    df = get_table_data(param1, param2, param3, param4, param5, param6)
    df.to_excel(writer, index=False, sheet_name='sheetName')
 
    sheet_table_1 = writer.sheets['sheetName']
    for col_num, value in enumerate(df.columns.values):
        sheet_table_1.write(0, col_num, value, header_fmt)
    sheet_table_1.set_column('A:A', 10)
    sheet_table_1.set_column('B:B', 25)
    sheet_table_1.set_column('C:C', 35)
    sheet_table_1.set_column('D:D', 40)
    sheet_table_1.set_column('E:E', 35)
    sheet_table_1.set_column('F:F', 15)
    sheet_table_1.set_column('G:G', 40)
    workbook.close()
    writer.close()
    output.seek(0)
    return send_file(output, attachment_filename="FileName.xlsx", as_attachment=True)
```

4. oracle pagination
   
oracle分页的sql网上可以搜到很多
