---
title: python发送邮件（qq）
date: 2020-06-09 13:58:55
tags:
- smtp
- python
categories:
---

```python
import json
import smtplib
from email.mime.application import MIMEApplication
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from os.path import basename
 
import pandas as pd
 
 
def send_email():
    data = [{'name': 'jalen', 'link': 'www.baidu.com'},
            {'name': 'kitty', 'link': '<a href="" style="color: red;">http://jalen.site</a>'}]
    df_report = pd.DataFrame(data)
    report_html = df_report.to_html(index=False, border=1, justify='center', na_rep="", escape=False)
    report_html = report_html.replace('class', 'cellspacing=\"0\" class')
    header = '<html><body><p>Hi Jalen,</p>'
    neck = '<p>This is a test email. Please verify.</p>'
    footer = '<br><br>Thanks<br>HFS system</body></html>'
    email_body = header + neck + report_html + footer
 
    msg = MIMEMultipart('alternative')
    msg['From'] = 'xxx@qq.com'  # email sender
    msg['To'] = 'xxx@qq.com'  # email receiver
    msg['Subject'] = "Send Email Test"  # subject
    file_name = 'test.json'
    with open(file_name, "w") as f:
        json.dump(data, f)
    with open(file_name, "rb") as fil:
        part = MIMEApplication(
            fil.read(),
            Name=basename(file_name)
        )
    part['Content-Disposition'] = 'attachment; filename="%s"' % basename(file_name)
    msg.attach(part)
    msg_text = MIMEText(email_body, 'html', 'utf-8')
    msg.attach(msg_text)
 
    smtp = smtplib.SMTP_SSL('smtp.qq.com')
    smtp.set_debuglevel(1)
    smtp.ehlo('smtp.qq.com')
    qq_auth_code = 'xxxx'  # generated qq auth code
    smtp.login(msg['From'], qq_auth_code)
    smtp.sendmail(msg['From'], msg['To'].split(";"), msg.as_string())
    smtp.quit()
 
 
if __name__ == '__main__':
    send_email()
```
