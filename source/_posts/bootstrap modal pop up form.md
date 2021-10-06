---
title: bootstrap modal pop up form
date: 2019-03-04 09:21:37
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. modal box. when click button, pop up a modal form, this form contains date input and select box 

```
    <div class="col-sm-1">
        <button type="button" data-toggle="modal" data-target="#modalExportForm" class="btn btn-default" id="exportNav">
            Export
        </button>
    </div>
 
    <form action="{{ url_for('app.export') }}" method="get" id="exportForm">
        <div class="modal fade" id="modalExportForm" tabindex="-1" role="dialog" aria-labelledby="myModalLabel"
             aria-hidden="true">
            <div class="modal-dialog modal-sm" role="document">
                <div class="modal-content">
                    <div class="modal-header text-center">
                        <h4 class="modal-title w-100 font-weight-bold">Export Option</h4>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body mx-3">
                        <div class="md-form mb-5">
                            <i class="fas fa-envelope prefix grey-text"></i>
                            <label data-error="wrong" data-success="right" for="defaultForm-email">Date From:</label>
                            <span class='input-group date input-group-sm' id="fromDatePickerPop">
                            <input title="" type="text" value="" class="form-control" id="dateFrom" name="dateFrom">
                            <span class="input-group-addon">
                                <span class="glyphicon glyphicon-calendar"></span>
                            </span>
                        </span>
                        </div>
 
                        <div class="md-form mb-4">
                            <i class="fas fa-lock prefix grey-text"></i>
                            <label data-error="wrong" data-success="right" for="defaultForm-pass">Date To:</label>
                            <span class='input-group date input-group-sm' id="toDatePickerPop">
                            <input title="" type="text" value="" class="form-control" id="dateTo" name="dateTo">
                            <span class="input-group-addon">
                                <span class="glyphicon glyphicon-calendar"></span>
                            </span>
                        </span>
                        </div>
 
                        <div class="md-form mb-4">
                            <i class="fas fa-lock prefix grey-text"></i>
                            <label data-error="wrong" data-success="right" for="defaultForm-pass">Export Type:</label>
                            <select class="form-control selectpicker" multiple title="" name="exportType">
                                <option value="1" selected="selected">Option1</option>
                                <option value="2">Option2</option>
                                <option value="3">Option3</option>
                            </select>
                        </div>
                    </div>
                    <div class="modal-footer d-flex justify-content-center">
                        <button class="btn btn-default">Export</button>
                        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
                    </div>
                </div>
            </div>
        </div>
    </form>
```

2. set default value for data input area. 

```javascript
function initialDateInput() {
    var fromDay = moment(new Date());
    var endDay = moment(new Date());
    fromDay.subtract(90, 'days');
 
    $('#fromDatePickerPop').datetimepicker({
        useCurrent: false,
        format: 'YYYY-MM-DD 00:00:00',
        defaultDate: fromDay
    }).on('dp.change', function (e) {
        var incrementDay = moment(new Date(e.date));
        $('#toDatePicker').data('DateTimePicker').minDate(incrementDay);
        $(this).data('DateTimePicker').hide();
    });
 
    $("#toDatePickerPop").datetimepicker({
        useCurrent: false,
        format: 'YYYY-MM-DD 23:59:59',
        defaultDate: endDay
    }).on('dp.change', function (e) {
        var decrementDay = moment(new Date(e.date));
        $('#fromDatePicker').data('DateTimePicker').maxDate(decrementDay);
        $(this).data('DateTimePicker').hide();
    });
}
```

3. import

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-datetimepicker/4.17.47/js/bootstrap-datetimepicker.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.15.1/moment.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-select/1.12.4/js/bootstrap-select.min.js"></script>
<link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-datetimepicker/4.17.47/css/bootstrap-datetimepicker.min.css" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-select/1.12.4/css/bootstrap-select.min.css">
<script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
```
