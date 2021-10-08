---
title: 获取文件路径URL
date: 2018-03-30 15:20:25
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---


```java
                // jar包中运行，URL直接使用相对路径；如果url为空，则取绝对路径，此时可能是从本地编辑器运行
				URL url = this.getClass().getResource(path);
				if ( null==url ){
					String clazzPath = this.getClass().getResource("/").getPath();
					path = "file://"+ clazzPath + path;
					url = new URL(path);
				}
```
