---
title: jquery drag and drop
date: 2019-04-03 14:27:51
tags:
- jquery
- html
categories:
- [学习, 前端知识学习, JQuery]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. drag.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style type="text/css">
        .drag-area {
            width: 205px;
            height: 500px;
            background: #fff;
            display: inline-block;
            vertical-align: top;
            position: relative;
            margin-left: 30px;
            border: 1px solid #ddd;
            box-shadow: 3px 3px 6px 3px rgba(0, 0, 0, 0.06);
        }
 
        .area {
            position: absolute;
            margin: 0 auto;
            color: #ccc;
            font-size: 20px;
            bottom: 10px;
            left: 20px;
        }
 
        .box {
            width: 200px;
            height: 20px;
            background: #37A3CF;
            color: #fff;
            text-align: center;
            z-index: 2;
            border: 2px solid #37A3CF;
 
        }
 
        .result {
            display: inline-block;
            margin-left: 30px;
        }
    </style>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/jquery-ui-bootstrap/0.5pre/assets/css/bootstrap.min.css">
    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>
    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jqueryui/1.12.1/jquery-ui.min.js"></script>
    <script type="text/javascript" src="drag.js"></script>
</head>
<body>
    <div>
        <div class="drag-area">
            <div class="area">Select Items</div>
            <div class="box">box1</div>
            <div class="box">box2</div>
            <div class="box">box3</div>
            <div class="box">box4</div>
            <div class="box">box5</div>
            <div class="box">box6</div>
            <div class="box">box7</div>
            <div class="box">box8</div>
        </div>
        <div class="drag-area">
            <div class="area">Enable</div>
        </div>
        <div class="drag-area">
            <div class="area">Disable</div>
        </div>
        <div class="drag-area">
            <div class="area">Remove</div>
        </div>
        <div class="result">-</div>
    </div>
</body>
</html>
```

2. drag.js

```javascript
$(function () {
    $(".box").draggable({
        scope: 'demoBox',
        revertDuration: 100,
        start: function (event, ui) {
            //Reset
            $(".box").draggable("option", "revert", true);
            $('.result').html('-');
        }
    });
 
    $(".drag-area").droppable({
        scope: 'demoBox',
        drop: function (event, ui) {
            var area = $(this).find(".area").html();
            var box = $(ui.draggable).html();
            $(".box").draggable("option", "revert", false);
 
            //Display action in text
            $('.result').html("[Action] <b>" + box + "</b>" +
                " dropped on " +
                "<b>" + area + "</b>");
 
            //Realign item
            $(ui.draggable).detach().css({top: 0, left: 0}).appendTo(this);
        }
    })
 
});
```

参考：https://codepen.io/jalenchu/pen/qwORXB
