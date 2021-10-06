---
title: Base64学习
date: 2019-07-10 15:07:49
tags:
- encode
- base64
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. Base64索引表

![base01](../images/base01.png)

2. ASCII码表

![base02](../images/base02.png)

3. 编码

（1）编码“Man” (注意Man包含3个字符，是3的倍数)

![base03](../images/base03.png)

（2）编码的字节数不能被3整除，结尾补0，若是000000，则编码为=

![base04](../images/base04.png)

### 参考

https://zh.wikipedia.org/wiki/Base64 
