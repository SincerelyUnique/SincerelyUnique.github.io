---
title: jquery jbox插件获取iframe中元素值
date: 2018-07-05 16:45:34
tags:
- jquery
- iframe
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

## iframe表单

```html
<form id="editActivateFrom" style="text-align: center;margin-top: 10px">
        <input id="projectId" name="projectId" value="${projectId}" type="hidden" >
        <textarea id="hangupReason" name="hangupReason" placeholder="请输入挂起原因" style="width: 500px;height: 200px"></textarea>
</form>
```

## js代码

```javascript
$.jBox.confirm("您确认要激活吗？", "提示", function(v,h,f){
            if( v===true ){
                $.ajax({
                    type: "GET",
                    url: ctx+'/acc/prj/checkActivateStatus?projectId='+rowData.id,
                    dataType: "json",
                    async: false,
                    success: function(data){
                        if( data.code===200 ){
                            $.jBox.open("iframe:"+ctx+"/approvalHangup/editActivatedReason?projectId="+rowData.id,
                                "审批挂起原因",550,320,{
                                    buttons: {"提交":true},submit:function(v, h, f){
                                        var projectId = h.find("iframe")[0].contentWindow.projectId.value;
                                        var hangupReason = h.find("iframe")[0].contentWindow.hangupReason.value;
                                    	$.ajax({
                                            type: "POST",
                                            url: ctx+'/acc/prj/activateProject',
                                            async: false,
											data: {
                                                projectId : projectId,
                                                hangupReason : hangupReason
											},
                                            dataType: "json",
                                            success: function(data){
                                                if( data.code===200 ){
                                                    $.jBox.tip( data.msg, 'success' );
                                                }else{
                                                    $.jBox.tip( data.msg, 'warning' );
                                                }
                                            },
                                            error: function(xhr) {
                                                $.jBox.tip( data.msg, 'error' );
                                            }
                                        });
                                    }, loaded:function(h){
                                        $("#jbox-content").css("overflow","hidden");
                                    }
                                });
                        }else{
                            $.jBox.tip( data.msg, 'warning' );
                        }
                    },
                    error: function(xhr) {
                        $.jBox.tip( data.msg, 'error' );
					}
                });
            }
}, { buttons: { '确定': true, '取消': false } });
```

重点是下面两行

```javascript
var projectId = h.find("iframe")[0].contentWindow.projectId.value;
var hangupReason = h.find("iframe")[0].contentWindow.hangupReason.value;
```

其中projectId对应iframe表单中的标签id，hangupReason也是一样

```javascript
ctx+"/approvalHangup/editActivatedReason
```

上面的这个请求就是嵌入该iframe的页面，需要走controller
