---
title: bootstrap4.3.1的datetimepicker没有icon图标
date: 2019-10-30 12:29:53
tags:
- bootstrap
categories:
- [学习, 前端知识学习, Bootstrap]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1.cdn

```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/4.3.1/css/bootstrap.css">
<link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-datetimepicker/4.17.47/css/bootstrap-datetimepicker.min.css" rel="stylesheet">
 
 
<script src="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/4.3.1/js/bootstrap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-datetimepicker/4.17.47/js/bootstrap-datetimepicker.min.js"></script>
```

2.html code （FYI: https://eonasdan.github.io/bootstrap-datetimepicker/）

```html
<div class="container">
    <div class="row">
        <div class='col-sm-6'>
            <div class="form-group">
                <div class='input-group date' id='datetimepicker1'>
                    <input type='text' class="form-control" />
                    <span class="input-group-addon">
                        <span class="glyphicon glyphicon-calendar"></span>
                    </span>
                </div>
            </div>
        </div>
        <script type="text/javascript">
            $(function () {
                $('#datetimepicker1').datetimepicker();
            });
        </script>
    </div>
</div>
```

3.reason (bootstrap3.x to bootstrap4.x changelog:  https://v4-alpha.getbootstrap.com/migration/#components)

```
1. Dropped panels, thumbnails, and wells for a new all-encompassing component, cards.
2. Dropped the Glyphicons icon font. If you need icons, some options are:
    the upstream version of Glyphicons
    Octicons
    Font Awesome
3. Dropped the Affix jQuery plugin. We recommend using a position: sticky polyfill instead. See the HTML5 Please entry for details and specific polyfill recommendations.
    If you were using Affix to apply additional, non-position styles, the polyfills might not support your use case. One option for such uses is the third-party ScrollPos-Styler library.
4. Dropped the pager component as it was essentially slightly customized buttons.
5. Refactored nearly all components to use more un-nested classes instead of children selectors.
```

4.modify (reference: https://fontawesome.com/v4.7.0/icon/calendar)

```html
<i class="fa fa-calendar" aria-hidden="true"></i>
```

5.reference

https://stackoverflow.com/questions/49905458/glyphicon-not-working-in-bootstrap-version-4-1-and-later
