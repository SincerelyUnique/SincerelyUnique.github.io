---
title: 抓取html table数据（python）
date: 2019-07-11 16:16:56
tags:
- python
- reptile
- html
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
def parse_html_table():
    """
    pip install bs4
    pip install lxml
    pip install html5lib
    pip install pandas
    """
    from bs4 import BeautifulSoup
    import pandas as pd
    import requests
 
    wiki_title = "%E4%B8%AD%E5%8D%8E%E4%BA%BA%E6%B0%91%E5%85%B1%E5%92%8C%E5%9B%BD%E5%9B%BD%E9%81%93"
    url = "https://zh.wikipedia.org/wiki/" + wiki_title
    response = requests.get(url)
    status_code, content = response.status_code, response.content
    print(status_code, content)
    soup = BeautifulSoup(content, 'html.parser')
    tables = soup.find_all('table')
 
    file_name = "export.xlsx"
    writer = pd.ExcelWriter(file_name, engine='xlsxwriter')
    workbook = writer.book
    for idx, table in enumerate(tables):
        if idx not in [2, 3, 4, 5]:
            continue
        table_title = 'Table-' + str(idx)
        df_table = pd.read_html(str(table), header=0, flavor='bs4')[0]
 
        df_table.to_excel(writer, index=False, sheet_name=table_title)
        worksheet = writer.sheets[table_title]
        header_fmt = workbook.add_format({'font_size': 14, 'bold': True, 'fg_color': '#D7E4BC', 'border': 1})
        for col_num, value in enumerate(df_table.columns.values):
            worksheet.write(0, col_num, value, header_fmt)
        worksheet.set_column('A:Z', 25)
    writer.save()
    print('Export End!')
 
 
if __name__ == '__main__':
    parse_html_table()
```
