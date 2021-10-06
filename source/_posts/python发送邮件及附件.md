---
title: python发送邮件及附件
date: 2019-03-20 13:35:45
tags:
- python
- email
- smtp
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
import datetime
import os
 
import xlsxwriter
import smtplib
from os.path import basename
from email.mime.application import MIMEApplication
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.utils import formatdate
 
 
def write_data_to_excel(file_name, data):
    wbk = xlsxwriter.Workbook(file_name)
    sheet_name = 'sheet1'
    sheet = wbk.add_worksheet(sheet_name)
    header = [u'a', u'b', u'c', u'd', u'e', u'f', u'g']
    h = 0
    for each_header in header:
        sheet.write(0, h, each_header)
        h += 1
 
    for i in range(len(data)):
        for j in range(len(data[i])):
            if j == 0:
                sheet.write(i + 1, j, u'%s' % data[i]['a'])
            if j == 1:
                sheet.write(i + 1, j, u'%s' % data[i]['b'])
            if j == 2:
                sheet.write(i + 1, j, u'%s' % data[i]['c'])
            if j == 3:
                sheet.write(i + 1, j, u'%s' % data[i]['d'])
            if j == 4:
                sheet.write(i + 1, j, u'%s' % data[i]['e'])
            if j == 5:
                sheet.write(i + 1, j, u'%s' % data[i]['f'])
            if j == 6:
                sheet.write(i + 1, j, u'%s' % data[i]['g'])
 
    wbk.close()
 
 
def send_email(file_name):
    msg = MIMEMultipart()
    msg['From'] = "xxx@xxx.com"
    msg['To'] = "xxx@xxx.com"
    msg['Date'] = formatdate(localtime=True)
    msg['Subject'] = "Test email with attachment"
    text = "Test text"
 
    msg.attach(MIMEText(text))
 
    with open(file_name, "rb") as fil:
        part = MIMEApplication(
            fil.read(),
            Name=basename(file_name)
        )
    part['Content-Disposition'] = 'attachment; filename="%s"' % basename(file_name)
    msg.attach(part)
 
    smtp = smtplib.SMTP('xxx_server')
    smtp.sendmail("xxx@xxx.com", "xxx@xxx.com", msg.as_string())
    smtp.close()
 
 
if __name__ == '__main__':
    from_date_str = '2018-01-10 00:00:00'
    to_date_str = '2018-01-15 00:00:00'
    today_date = datetime.datetime.now().strftime('%Y%m%d')
    file_name = 'prefix-' + str(today_date) + '.xlsx'
    data = get_export_data(from_date_str, to_date_str)
    write_data_to_excel(file_name, data)
    send_email(file_name)
    os.remove(file_name)
```

参考：https://blog.csdn.net/kangkangwanwan/article/details/84778984
