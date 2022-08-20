---
title: pandas excel下载
date: 2019-02-26 17:28:27
tags:
- python
- pandas
- excel
categories:
- [学习, Python语言学习, Pandas数据分析]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
@report.route('/export', methods=['GET'])
@login_required
def export():
    from io import BytesIO
    import pandas as pd
    
    # DataFrame data
    df_sheet1 = pd.DataFrame()
    df_sheet2 = pd.DataFrame()
    df_sheet3 = pd.DataFrame()
    df_sheet4 = pd.DataFrame()
    df_sheet5 = pd.DataFrame()
    
    output = BytesIO()
    writer = pd.ExcelWriter(output)
    df_sheet1.to_excel(writer, index=False, sheet_name='sheet1')
    df_sheet2.to_excel(writer, index=False, sheet_name='sheet2')
    df_sheet3.to_excel(writer, index=False, sheet_name='sheet3')
    df_sheet4.to_excel(writer, index=False, sheet_name='sheet4')
    df_sheet5.to_excel(writer, index=False, sheet_name='sheet5')
    workbook = writer.book
    header_fmt = workbook.add_format({
        'font_size': 14,
        'bold': True,
        'fg_color': '#D7E4BC',
        'border': 1})
 
    sheet_table_1 = writer.sheets['sheet1']
    for col_num, value in enumerate(df_sheet1.columns.values):
        sheet_table_1.write(0, col_num, value, header_fmt)
    sheet_table_1.set_column('A:A', 10)
    sheet_table_1.set_column('B:B', 25)
    sheet_table_1.set_column('C:C', 35)
    sheet_table_1.set_column('D:D', 40)
    sheet_table_1.set_column('E:E', 35)
    sheet_table_1.set_column('F:F', 15)
    sheet_table_1.set_column('G:G', 40)
 
    sheet_table_2 = writer.sheets['sheet2']
    for col_num, value in enumerate(df_sheet2.columns.values):
        sheet_table_2.write(0, col_num, value, header_fmt)
    sheet_table_2.set_column('A:A', 10)
    sheet_table_2.set_column('B:B', 25)
    sheet_table_2.set_column('C:C', 35)
    sheet_table_2.set_column('D:D', 15)
    sheet_table_2.set_column('E:E', 35)
    sheet_table_2.set_column('F:F', 15)
    sheet_table_2.set_column('G:G', 40)
 
    sheet_table_3 = writer.sheets['sheet3']
    for col_num, value in enumerate(df_sheet3.columns.values):
        sheet_table_3.write(0, col_num, value, header_fmt)
    sheet_table_3.set_column('A:A', 10)
    sheet_table_3.set_column('B:B', 25)
    sheet_table_3.set_column('C:C', 35)
    sheet_table_3.set_column('D:D', 15)
    sheet_table_3.set_column('E:E', 35)
    sheet_table_3.set_column('F:F', 15)
    sheet_table_3.set_column('G:G', 40)
 
    sheet_table_4 = writer.sheets['sheet4']
    for col_num, value in enumerate(df_sheet4.columns.values):
        sheet_table_4.write(0, col_num, value, header_fmt)
    sheet_table_4.set_column('A:A', 10)
    sheet_table_4.set_column('B:B', 25)
    sheet_table_4.set_column('C:C', 35)
    sheet_table_4.set_column('D:D', 15)
    sheet_table_4.set_column('E:E', 35)
    sheet_table_4.set_column('F:F', 15)
    sheet_table_4.set_column('G:G', 40)
 
    sheet_table_5 = writer.sheets['sheet5']
    for col_num, value in enumerate(df_sheet5.columns.values):
        sheet_table_5.write(0, col_num, value, header_fmt)
    sheet_table_5.set_column('A:A', 10)
    sheet_table_5.set_column('B:B', 25)
    sheet_table_5.set_column('C:C', 35)
    sheet_table_5.set_column('D:D', 15)
    sheet_table_5.set_column('E:E', 35)
    sheet_table_5.set_column('F:F', 15)
    sheet_table_5.set_column('G:G', 40)
    workbook.close()
    writer.close()
    output.seek(0)
    return send_file(output, attachment_filename="Name.xlsx", as_attachment=True)
```


