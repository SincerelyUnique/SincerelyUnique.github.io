---
title: python读取服务器日志
date: 2019-04-14 00:55:35
tags:
- python
- paramiko
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 代码 

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
    sftp_client = client.open_sftp()
    remote_file = sftp_client.open("/opt/xxx/logs/error_log")
    try:
        count = 0
        for line in remote_file:
            if 'Traceback' in line:
                print(line)
                count = count + 1
        print(count)
    finally:
        remote_file.close()
 
 
if __name__ == "__main__":
    get_server_log()
    print('****done')
```

2. 验证Traceback在日志文件出现次数

> grep -F "Traceback" -R logs/error_log

3. 普通用户登录时可能报sftp权限错误，可以先给改目录分配用户权限

> chown -R username:username logs/error_log

4. 如果不方便使用chown授权，但是可以当你用普通账号登录后可以切换成root用户，也可以这样写（就是sudo切换一下）：

```python
    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    client.connect(hostname, port, username, password, compress=True)
    if username != 'root':
        ssh = client.invoke_shell()
        time.sleep(0.1)
        ssh.send('sudo su \n')
        buff = ''
        while not buff.endswith('Enter domain (xxx) password:'): #  这里改成你自己的
            resp = ssh.recv(9999)
            buff += resp
        ssh.send(password)
        ssh.send('\n')
        buff = ''
        while not buff.endswith('# '):
            resp = ssh.recv(9999)
            buff += resp
        ssh.send(remote_command)
        ssh.send('\n')
        buff = ''
        while not buff.endswith('# '):
            resp = ssh.recv(9999)
            buff += resp
        client.close()
        result = buff
    else:
        stdin, stdout, stderr = client.exec_command(remote_command)
        result = stdout.read()
```
