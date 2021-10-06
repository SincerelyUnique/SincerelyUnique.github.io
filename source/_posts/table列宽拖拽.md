---
title: table列宽拖拽
date: 2019-05-29 16:37:41
tags:
- html
- js
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 使用第三方组件colResizable-1.6.min.js （官网：http://www.bacubacu.com/colresizable/）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-table/1.14.2/extensions/resizable/bootstrap-table-resizable.min.js"></script>
    <script src="../../static/js/plugins/colResizable-1.6.min.js"></script>
</head>
<body>
<table id="tb_3" cellspacing="0" cellpadding="2" width="100%" border="1">
    <tbody>
    <tr align="center" bgcolor="#dcdcdc">
        <td style="width:100px;">用户编号</td>
        <td>试用时间</td>
        <td>转正时间</td>
        <td>性别</td>
        <td>姓名拼音</td>
        <td>生日时间</td>
        <td>民族</td>
        <td>身高</td>
    </tr>
    <tr>
        <td>2000001</td>
        <td>1997-3-13</td>
        <td>1997-3-13</td>
        <td>1</td>
        <td>WZJ</td>
        <td>1965-3-13</td>
        <td>汉</td>
        <td>171</td>
    </tr>
    <tr>
        <td>2000045</td>
        <td>2001-2-15</td>
        <td>2001-3-15</td>
        <td>0</td>
        <td>WY</td>
        <td>1978-8-5</td>
        <td>汉</td>
        <td>162</td>
    </tr>
    <tr>
        <td>2000046</td>
        <td>2001-2-23</td>
        <td>2001-3-23</td>
        <td>0</td>
        <td>LQ</td>
        <td>2001-2-23</td>
        <td>汉</td>
        <td>171</td>
    </tr>
    </tbody>
</table>
<script type="text/javascript">
    $(function () {
        $("#tb_3").colResizable({
            liveDrag: true,
            gripInnerHtml: "<div class='grip'></div>",
            draggingClass: "dragging",
            resizeMode: 'fit'
        });
    });
</script>
</body>
</html>
```

2. 原生js （参考：https://blog.csdn.net/liangxw1/article/details/78144347）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<table id="tb_3" cellspacing="0" cellpadding="2" width="100%" border="1">
    <tbody>
    <tr align="center" bgcolor="#dcdcdc">
        <td style="width:100px;">用户编号</td>
        <td>试用时间</td>
        <td>转正时间</td>
        <td>性别</td>
        <td>姓名拼音</td>
        <td>生日时间</td>
        <td>民族</td>
        <td>身高</td>
    </tr>
    <tr>
        <td>2000001</td>
        <td>1997-3-13</td>
        <td>1997-3-13</td>
        <td>1</td>
        <td>WZJ</td>
        <td>1965-3-13</td>
        <td>汉</td>
        <td>171</td>
    </tr>
    <tr>
        <td>2000045</td>
        <td>2001-2-15</td>
        <td>2001-3-15</td>
        <td>0</td>
        <td>WY</td>
        <td>1978-8-5</td>
        <td>汉</td>
        <td>162</td>
    </tr>
    <tr>
        <td>2000046</td>
        <td>2001-2-23</td>
        <td>2001-3-23</td>
        <td>0</td>
        <td>LQ</td>
        <td>2001-2-23</td>
        <td>汉</td>
        <td>171</td>
    </tr>
    </tbody>
</table>
<script type="text/javascript">
    var tTD; //用来存储当前更改宽度的Table Cell,避免快速移动鼠标的问题
    var table = document.getElementById("tb_3");
    for (j = 0; j < table.rows[0].cells.length; j++) {
        table.rows[0].cells[j].onmousedown = function () {
            //记录单元格
            tTD = this;
            if (event.offsetX > tTD.offsetWidth - 10) {
                tTD.mouseDown = true;
                tTD.oldX = event.x;
                tTD.oldWidth = tTD.offsetWidth;
            }
            //记录Table宽度
            //table = tTD; while (table.tagName != ‘TABLE') table = table.parentElement;
            //tTD.tableWidth = table.offsetWidth;
        };
        table.rows[0].cells[j].onmouseup = function () {
            //结束宽度调整
            if (tTD === undefined) tTD = this;
            tTD.mouseDown = false;
            tTD.style.cursor = 'default';
        };
        table.rows[0].cells[j].onmousemove = function () {
            //更改鼠标样式
            if (event.offsetX > this.offsetWidth - 10)
                this.style.cursor = 'col-resize';
            else
                this.style.cursor = 'default';
            //取出暂存的Table Cell
            if (tTD === undefined) tTD = this;
            //调整宽度
            if (tTD.mouseDown != null && tTD.mouseDown === true) {
                tTD.style.cursor = 'default';
                if (tTD.oldWidth + (event.x - tTD.oldX) > 0)
                    tTD.width = tTD.oldWidth + (event.x - tTD.oldX);
                //调整列宽
                tTD.style.width = tTD.width;
                tTD.style.cursor = 'col-resize';
                //调整该列中的每个Cell
                table = tTD;
                while (table.tagName !== 'TABLE') table = table.parentElement;
                for (j = 0; j < table.rows.length; j++) {
                    table.rows[j].cells[tTD.cellIndex].width = tTD.width;
                }
                //调整整个表
                //table.width = tTD.tableWidth + (tTD.offsetWidth – tTD.oldWidth);
                //table.style.width = table.width;
            }
        };
    }
</script>
</body>
</html>
```
