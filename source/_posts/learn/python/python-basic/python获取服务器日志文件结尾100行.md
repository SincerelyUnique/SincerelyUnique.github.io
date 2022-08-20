---
title: python获取服务器日志文件结尾100行
date: 2019-04-14 01:14:58
tags:
- python
- paramiko
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
import paramiko
 
 
def get_server_log():
    hostname = ""
    port = 22
    username = ""
    password = ""
    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    client.connect(hostname, port, username, password, compress=True)
    remote_command = "tail -n100 /opt/xxx/logs/error_log"
    stdin, stdout, stderr = client.exec_command(remote_command)
    last_line = stdout.read()
    try:
        count = 0
        for line in last_line:
            if 'Traceback' in line:
                print(line)
                count = count + 1
        print(count)
    finally:
        last_line.close()
 
 
if __name__ == "__main__":
    get_server_log()
    print('****done')
```
