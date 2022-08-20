---
title: RawGit has reached the end of its useful life
date: 2019-08-22 14:10:58
tags:
- RawGit
categories:
- [学习, 前端知识学习, Bootstrap]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

通常在我们的html里会引入一些CDN，这里的rawgit是github上的一款开源插件的CDN分发平台，既然开源，如果安全审查不够严谨，就会存在一些恶意的内容分发，其实这个小工具只是作者因为个人爱好开源的，但是现在月访问量已达到几十亿，带宽达到几百TB，作者也是为用户考虑，即将关闭该CDN，并给出了几个可供选择的git CDN。（作者说明：https://rawgit.com/）

比如我们程序里用到了bootstraptable扩展导出插件，因为在一些主流的CDN找不到，所以只能在git上找一些开源的组件

原CDN

```html
    <script src="https://rawgit.com/hhurz/tableExport.jquery.plugin/master/tableExport.js"></script>
    <script src="https://rawgit.com/hhurz/tableExport.jquery.plugin/master/libs/FileSaver/FileSaver.min.js"></script>
    <script src="https://rawgit.com/hhurz/tableExport.jquery.plugin/master/libs/js-xlsx/xlsx.core.min.js"></script>
```

改为

```html
    <script src="https://cdn.jsdelivr.net/gh/hhurz/tableExport.jquery.plugin@master/tableExport.js"></script>
    <script src="https://cdn.jsdelivr.net/gh/hhurz/tableExport.jquery.plugin@master/libs/FileSaver/FileSaver.min.js"></script>
    <script src="https://cdn.jsdelivr.net/gh/hhurz/tableExport.jquery.plugin@master/libs/js-xlsx/xlsx.core.min.js"></script>
```

四种可供选择的CDN

[jsDelivr](https://www.jsdelivr.com/rawgit)
[GitHub Pages](https://pages.github.com/)
[CodeSandbox](https://codesandbox.io/)
[unpkg](https://unpkg.com/)
