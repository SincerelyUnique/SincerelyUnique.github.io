---
title: easyui下拉框复制粘贴时遇到的不显示问题
date: 2017-12-14 10:26:40
tags:
- easyui
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 背景

使用easyui-combobox，众所周知，combobox是以id，name形式（或key/value）存在的，而从页面上进行如下操作时偶尔会遇到一个问题：

> 复制一条下拉框已包含的数据到下拉框中，不要使用鼠标点击下拉框加载的内容，点击保存按钮时，并没有保存name；

下拉框代码：

```html
    <td>
        <input id='contract_Departname' name='contract_Departname' type='hidden' value='${model.contract_Departname}'>
        <input style="width: 200px;" name="contract_Departid" id="contract_Departid" class="easyui-combobox"
               data-options="url:'${root}/review/get.do',validType:['checkComboboxValue'],editable:true,valueField: 'id',textField: 'department',prompt:'请选择',
               data:[{id:'${model.contract_Departid}',selected:true}]"/>
        <span id="contractDepartidWarnSpan" class="red">*</span>
    </td>
```

```javascript
    $("#contract_Departid").combobox({
        onSelect : function(record) {
            if (record !== undefined) {
                $("#contract_Departname").val(record.department);
            }
        }
    });
```

提交时参数获取方式：

```javascript
var param = $("#form").serialize()
```

解决方法：

在提交的js方法中添加如下代码

```javascript
$("#contract_Departname").val($("#contract_Departid").combobox("getText"));
```


