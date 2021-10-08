---
title: oracle错误提示编码问题解决记录（PLSQL编辑器执行脚本错误提示乱码）
date: 2018-10-08 14:41:24
tags:
- oracle
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

错误原因：oracle服务器编码与客户端编码不一致

解决办法：修改客户端编码（也可以修改服务器端编码，但是服务器一般都是一个局域网所有人共同连接的，私自修改会给他人带来不便）

解决步骤：

（1）查询服务器端编码

```sql
select * from nls_database_parameters
```

（2）查看官方提供的文档

地址：https://docs.oracle.com/cd/A84870_01/doc/nt.816/a73010/appc.htm

（3）修改本地的环境变量

![nls01](../images/nls01.png)

（4）重启pl/sql，再次运行，错误提示正常 
