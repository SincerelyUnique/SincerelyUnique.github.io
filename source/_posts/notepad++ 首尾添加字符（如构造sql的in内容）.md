---
title: notepad++ 首尾添加字符（如构造sql的in内容）
date:  2019-06-20 13:54:52
tags:
- notepad++
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 在每行开始添加单引号，光标默认在第一行首位，按住alt + shift + 方向键下拉，选中多行后直接输入单引号字符

2. 选择View -> Show Symbol -> Show End of Line, 末尾会出现CR LF字样，Ctrl + F选择Replace，输入“\r\n”, 替换成“',” 即可

演示：

![notepad01](../images/notepad01.gif)

（或者替换^，代表替换行首，替换$，代表替换行尾）
