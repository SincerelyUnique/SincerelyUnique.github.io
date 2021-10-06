---
title: form标签内使用button建议指定type
date: 2019-02-12 10:46:34
tags:
- html
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

原因：

Internet Explorer 的默认类型是 "button"，而其他浏览器中（包括 W3C 规范）的默认值是 "submit"。

当type为submit时，默认向后台请求，导致页面刷新

 

测试：

以动态添加input输入框为例，一旦去掉type属性会出现问题

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
    <script type="text/javascript">
        $(document).ready(function(){
            $("button").click(function(){
                var html = "<br><div id=\"son\"><input title=\"\" type=\"text\"></div>";
                $("#parent").append(html);
            });
        });
    </script>
</head>
<body>
    <form action="" method="post">
        <div id="parent">
            <div id="son">
                <input title="" type="text">
            </div>
        </div>
        <br/>
        <!-- 可以将下面的type属性去掉尝试 -->
        <button id="button" type="button">Button</button>
    </form>
</body>
</html>
```

参考：

1. W3C关于button的介绍：http://www.w3school.com.cn/tags/tag_button.asp
