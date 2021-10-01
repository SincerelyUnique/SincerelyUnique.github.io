---
title: webstorm eslint error ESLint Expected indentation of 0 spaces but found 2. (indent)
date: 2019-12-29 00:45:42
tags:
- webstorm
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1.原因

![ide01](../images/ide01.png)

2.参考

https://github.com/vuejs/eslint-plugin-vue/issues/352

3.解决

（1） 编辑Eslint配置，忽略缩进：'indent': 'off'

（2） 编辑webstorm规则，忽略script子标签缩进，File => Setting => Editor => Code Style => HTML 找到 add Do not indent children of 的选项，添加 script 标签。然后code=>Reformat Code即可

![ide02](../images/ide02.png)
