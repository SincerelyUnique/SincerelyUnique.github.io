---
title: 使用Spring的ResponseEntity.ok().header下载文件时中文乱码解决
date: 2018-04-10 16:24:38
tags:
- spring
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```java
    @GetMapping("/files/{filename:.+}")
    @ResponseBody
    public ResponseEntity<Resource> serveFile(@PathVariable String filename) {
 
        Resource file = storageService.loadAsResource(filename);
 
        String[] strArr = filename.split("\\.");
        String fileNamePrefix = strArr[0];
        String fileNameSuffix = strArr[1];
        String finalFileName = null;
        try {
            finalFileName = URLEncoder.encode(fileNamePrefix,"UTF-8")+"."+fileNameSuffix;
        }catch (UnsupportedEncodingException e){
            log.info("文件名字转换异常");
            e.printStackTrace();
        }
 
        return ResponseEntity.ok().header(HttpHeaders.CONTENT_DISPOSITION,
                "attachment; filename=\"" + finalFileName + "\"").body(file);
    }
```

参考：https://yq.aliyun.com/articles/38945
