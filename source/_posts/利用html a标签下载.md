---
title: 利用html a标签下载
date: 2019-09-23 09:30:22
tags:
- html
- js
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

（1）链接的 onclick 事件被先执行，其次是 href 属性下的动作（页面跳转，或 javascript 伪链接）

（2）假设链接中同时存在 href 与 onclick，如果想让 href 属性下的动作不执行，onclick 必须得到一个 false 的返回值。

（3）如果页面过长有滚动条，且希望通过链接的 onclick 事件执行操作。应将它的 href 属性设为 javascript:void(0);，而不要是 #，这可以防止不必要的页面跳动；

（4）如果在链接的 href 属性中调用一个有返回值的函数，当前页面的内容将被此函数的返回值代替；

```html
<a href="javascript:void(0);" onclick="download()" style="color: blue" download>Download</a>
```

```javascript
function download() {
    let url = window.location.protocol + '//' + window.location.host + '/download';
    url = url + '?userAge=' + $("#userAge").val();
    url = url + '&userId='+$("#userId").val();
    url = url + '&userName='+encodeURIComponent(encodeURIComponent($("#userName").val()));
    window.location.href = url;
    return false;
}
```

```python
@xxx.route('/download', methods=['GET'])
@login_required
def download():
    user_id = request.args.get('userId')
    user_age = request.args.get('userAge')
    user_name = urlparse.unquote(request.args.get('userName'))
    logger.info('userId: {0}, userAge: {1}, userName: {2}'.format(str(user_id), str(user_age), str(user_name)))
    connection = engine.raw_connection()
    df = pd.DataFrame()
    try:
        cursor = connection.cursor()
        cursor.callproc("xxx_xxxx", [user_name, user_age])
        sql = "select * from  xxx_xxx where userid=:user_id "
        df = pd.read_sql_query(sql, engine, params=[user_id])
        cursor.close()
        connection.commit()
    except Exception as e:
        logger.error('Get data error:' + str(e.message))
        logger.error(traceback.format_exc())
    finally:
        connection.close()
 
    output = BytesIO()
    writer = pd.ExcelWriter(output)
    df.to_excel(writer, index=False, sheet_name='Sheet')
    workbook = writer.book
    header_fmt = workbook.add_format({
        'font_size': 14,
        'bold': True,
        'fg_color': '#D7E4BC',
        'border': 1})
    sheet_table = writer.sheets['Sheet']
    for col_num, value in enumerate(df.columns.values):
        sheet_table.write(0, col_num, value, header_fmt)
    sheet_table.set_column('A:A', 30)
    sheet_table.set_column('B:B', 10)
    sheet_table.set_column('C:C', 30)
    sheet_table.set_column('D:I', 15)
    sheet_table.set_column('J:K', 20)
    workbook.close()
    writer.close()
    output.seek(0)
    return send_file(output, attachment_filename="Excel_Name.xlsx", as_attachment=True)
```
