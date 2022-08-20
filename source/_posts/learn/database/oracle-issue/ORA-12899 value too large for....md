---
title: ORA-12899 value too large for...
date: 2017-11-10 09:58:34
tags:
- oracle
- sql
categories:
- [学习, 数据库学习, Oracle问题记录]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

数据库字段设置问题，这里是文件路径长度不足

# 字段长度不足

原因:

```
### Error updating database.  Cause: java.sql.SQLException: ORA-12899: value too large for column "IFLYYJY"."FILE_UPLOAD"."FILEPATH" (actual: 56, maximum: 50)
 
### The error may exist in file [/app/tomcat_gps/apache-tomcat-7.0.79/webapps/ifly-web-gps/WEB-INF/classes/com/iflytek/gps/gps/entity/mapper/GPSFileUploadMapper.xml]
### The error may involve com.iflytek.gps.gps.dao.GPSFileUploadMapper.insertFileData-Inline
### The error occurred while setting parameters
### SQL: INSERT INTO FILE_UPLOAD (ID,fileName, fileLength, fileStoreName,         filePath, isHandled,createTime)         VALUES (?,?, ?, ?,         ?, ?,?)
### Cause: java.sql.SQLException: ORA-12899: value too large for column "IFLYYJY"."FILE_UPLOAD"."FILEPATH" (actual: 56, maximum: 50)
```

修改：

将FILE_UPLOAD表中FILEPATH字段长度修改为100或更长。
