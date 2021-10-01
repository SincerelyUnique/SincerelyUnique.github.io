---
title: 2019-10-21 21:24:03
date: 2015-02-22 14:34:43
tags:
- openssl
- certificates
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

背景：本地python flask项目，python脚本运行manage.py启动server，使用ngrok配置内网穿透无法模拟https地址(报502)

```
ngrok http -bind-tls=true localhost:443
 
ngrok http https://localhost:443
 
ngrok http 443
```

现做以下尝试：

1.安装Openssl（参考地址：https://tecadmin.net/install-openssl-on-windows/ ）

下载地址：http://slproweb.com/products/Win32OpenSSL.html  （windows openssl下载地址，下载43M那个）

配置环境变量，bin目录添加到path以方便使用：

```
set OPENSSL_CONF=C:\Program Files\OpenSSL-Win64\bin\openssl.cfg
set Path=......Other Values here......;C:\Program Files\OpenSSL-Win64\bin
```

检测是否安装成功

```
Microsoft Windows [Version 10.0.17134.1069]
(c) 2018 Microsoft Corporation. All rights reserved.
 
C:\Users\jalchu>openssl
OpenSSL> version
OpenSSL 1.1.1d  10 Sep 2019
OpenSSL>
```

2.创建私钥 （参考：https://zeropointdevelopment.com/how-to-get-https-working-in-windows-10-localhost-dev-environment/ ）

```
PS D:\ssl> openssl genrsa -des3 -out rootSSL.key 2048
Generating RSA private key, 2048 bit long modulus (2 primes)
.........................................................+++++
...+++++
e is 65537 (0x010001)
Enter pass phrase for rootSSL.key:
Verifying - Enter pass phrase for rootSSL.key:
```

（注：上面的两个key输入相同的字符串）

3.创建证书 （参考：https://zeropointdevelopment.com/how-to-get-https-working-in-windows-10-localhost-dev-environment/ ）

```
PS D:\ssl> openssl req -x509 -new -nodes -key rootSSL.key -sha256 -days 1024 -out rootSSL.pem
Enter pass phrase for rootSSL.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:AU
State or Province Name (full name) [Some-State]:NSW
Locality Name (eg, city) []:Sydney
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Zero Point Development
Organizational Unit Name (eg, section) []:Development
Common Name (e.g. server FQDN or YOUR name) []:zeropointdevelopment.com
Email Address []:hello@zeropointdevelopment.com
PS D:\ssl> openssl req -new -sha256 -nodes -out client-1.local.csr -newkey rsa:2048 -keyout client-1.local.key -subj "/C
=AU/ST=NSW/L=Sydney/O=Client One/OU=Dev/CN=client-1/emailAddress=hello@client-1.local"
Generating a RSA private key
.............................................................................................+++++
.......................................................................................................+++++
writing new private key to 'client-1.local.key'
-----
```

4.证书授信 （参考：https://zeropointdevelopment.com/how-to-get-https-working-in-windows-10-localhost-dev-environment/ ）

```
（1）Step 1 – Press the Windows key + R
（2）Step 2 – Type “MMC” and click “OK”
（3）Step 3 – Go to “File > Add/Remove Snap-in”
（4）Step 4 – Click “Certificates” and “Add”
（5）Step 5 – Select “Computer Account” and click “Next”
（6）Step 6 – Select “Local Computer” then click “Finish”
（7）Step 7 – Click “OK” to go back to the MMC window
（8）Step 8 – Double-click “Certificates (local computer)” to expand the view
（9）Step 9 – Select “Trusted Root Certification Authorities”, right-click “Certificates” and select “All Tasks” then “Import”
（10）Step 10 – Click “Next” then Browse and locate the “rootSSL.pem” file we created in step 2
（11）Step 11 – Select “Place all certificates in the following store” and select the “Trusted Root Certification Authorities store”. Click “Next” then click “Finish” to complete the wizard.
```

5.映射Domain

修改C:\Windows\System32\drivers\etc\hosts，结尾添加

```
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host
 
# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost
 
127.0.0.1 client-1.local
```

6.给Domain创建私钥

```
PS D:\ssl> openssl req -new -sha256 -nodes -out client-1.local.csr -newkey rsa:2048 -keyout client-1.local.key -subj "/C
=AU/ST=NSW/L=Sydney/O=Client One/OU=Dev/CN=client-1/emailAddress=hello@client-1.local"
Generating a RSA private key
.............................................................................................+++++
.......................................................................................................+++++
writing new private key to 'client-1.local.key'
-----
```

7.发布证书

```
PS D:\ssl> openssl x509 -req -in client-1.local.csr -CA rootSSL.pem -CAkey rootSSL.key -CAcreateserial -out client-1.loc
al.crt -days 500 -sha256 -extensions "authorityKeyIdentifier=keyid,issuer\n basicConstraints=CA:FALSE\n keyUsage = digit
alSignature, nonRepudiation, keyEncipherment, dataEncipherment\n  subjectAltName=DNS:client-1.local"
Signature ok
subject=C = AU, ST = NSW, L = Sydney, O = Client One, OU = Dev, CN = client-1, emailAddress = hello@client-1.local
Getting CA Private Key
Enter pass phrase for rootSSL.key:
PS D:\ssl>
```

8.Nginx配置

```
	server {
		listen       8080;
		server_name  client-1.local;
 
		# New Lines below
		listen 443 ssl;
                ssl on;
		ssl_certificate d:/ssl/client-1.local.crt;
		ssl_certificate_key d:/ssl/client-1.local.key;
	}
```

启动/关闭命令

```
C:\Users\jalchu\Nginx\nginx-1.15.8> .\nginx.exe
C:\Users\jalchu\Nginx\nginx-1.15.8> .\nginx.exe -s stop
```

9.配置Pycharm启动参数，启动pycharm

> runserver --host 127.0.0.1 --port 8080 --threaded

10.Nginx启动报错

```
PS C:\Users\jalchu\Nginx\nginx-1.15.8> .\nginx.exe
nginx: [warn] the "ssl" directive is deprecated, use the "listen ... ssl" directive instead in C:\Users\jalchu\Nginx\nginx-1.15.8/conf/nginx.conf:128
nginx: [emerg] bind() to 0.0.0.0:443 failed (10013: An attempt was made to access a socket in a way forbidden by its access permissions)
```

第一个：高版本配置ssl时警告，可以移除掉ssl on;那一行

第二个：本地端口冲突，检查如下（也可以用任务管理器关闭该进程）

```
C:\Users\jalchu>netstat -aon|findstr "443"
  TCP    0.0.0.0:443            0.0.0.0:0              LISTENING       8716
  TCP    10.79.100.111:51627    66.163.36.181:443      ESTABLISHED     14904
  TCP    10.79.100.111:52294    103.116.4.197:443      CLOSE_WAIT      2384
  TCP    10.79.100.111:52313    103.116.4.197:443      CLOSE_WAIT      2384
  TCP    10.79.100.111:52956    13.59.223.131:443      ESTABLISHED     14904
  ... ...
C:\Users\jalchu>tasklist | findstr 8716
vmware-hostd.exe              8716 Services                   0      5,516 K
C:\Users\jalchu>tskill 8716
```

11.重启，访问页面，公司内网给block了，白忙活一场 :)

> SECURITY THREAT DETECTED AND BLOCKED
