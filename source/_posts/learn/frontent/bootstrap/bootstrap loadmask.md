---
title: bootstrap loadmask
date: 2018-11-09 16:55:45
tags:
- bootstrap
categories:
- [学习, 前端知识学习, Bootstrap]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 方式一

### (1)在html的body中添加如下代码

```html
<div class="modal fade" id="loadingModal">
	<div style="width: 200px;height:20px; z-index: 20000; position: absolute; text-align: center; left: 50%; top: 50%;margin-left:-100px;margin-top:-10px">
		<div class="progress progress-striped active" style="margin-bottom: 0;">
			<div class="progress-bar" style="width: 100%;"></div>
		</div>
		<h5>正在加载...</h5>
	</div>
</div>
```

### (2)用jquery进行显示和隐藏

```javascript
//显示
$("#loadingModal").modal('show');
//隐藏
$("#loadingModal").modal('hide');
```

### (3)其他设置

```javascript
//使点击空白处遮罩层不会消失 
$("#loadingModal").modal({backdrop:'static'});
//按Tab键遮罩层不会消失 ，默认值为true 
$("#loadingModal").modal({keyboard:false});
 
//也可以一起运用
//backdrop 为 static 时，点击模态对话框的外部区域不会将其关闭。
//keyboard 为 false 时，按下 Esc 键不会关闭 Modal。
$('#loadingModal').modal({backdrop: 'static', keyboard: false});
```

# 方式二（添加图片loader.gif）

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>遮罩层DEM</title>
    <!-- Bootstrap -->
    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
    <style type="text/css">
        .loading {
            background: #00000040 url(loader.gif) no-repeat center 50%;
            z-index: 9999;
        }
    </style>
</head>
 
<body>
    <div class="container-fluid text-center">
        <h2>遮罩层DEMO</h2>
        <!-- 按钮触发模态框 -->
        <button onclick="startup()">遮罩层</button>
        <!-- 遮罩层模态框（Modal） -->
        <div class="modal fade loading" 
             id="myModal" 
             tabindex="-1" 
             role="dialog" 
             aria-labelledby="myModalLabel"
             aria-hidden="true" 
             data-backdrop="static" 
             data-keyboard="false">
            // data-backdrop="static" data-keyboard="false" // 以上属性是静止掉了模态框的关闭按钮和点击空白处关闭模态框
        </div>
    </div>
        
    <script>
        function startup() {
            $("#myModal").modal('show');
            setTimeout('shutdown()', 5000);
        }
 
        function shutdown() {
            $("#myModal").modal('hide');
        }
    </script>
</body>
</html>
```
