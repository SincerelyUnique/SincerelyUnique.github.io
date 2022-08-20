---
title: ORA-01795 maximum number of expressions in a list is 1000 SQL异常
date: 2017-09-30 10:37:20
tags:
- oracle
- sql
categories:
- [学习, 数据库学习, Oracle问题记录]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
 org.springframework.jdbc.BadSqlGrammarException: 
### Error querying database.  Cause: java.sql.SQLSyntaxErrorException: ORA-01795: maximum number of expressions in a list is 1000
 
### Cause: java.sql.SQLSyntaxErrorException: ORA-01795: maximum number of expressions in a list is 1000
 
 
; bad SQL grammar []; nested exception is java.sql.SQLSyntaxErrorException: ORA-01795: maximum number of expressions in a list is 1000
 
 
	at org.springframework.jdbc.support.SQLExceptionSubclassTranslator.doTranslate(SQLExceptionSubclassTranslator.java:94)
	at org.springframework.jdbc.support.AbstractFallbackSQLExceptionTranslator.translate(AbstractFallbackSQLExceptionTranslator.java:72)
	at org.springframework.jdbc.support.AbstractFallbackSQLExceptionTranslator.translate(AbstractFallbackSQLExceptionTranslator.java:80)
	at org.mybatis.spring.MyBatisExceptionTranslator.translateExceptionIfPossible(MyBatisExceptionTranslator.java:71)
	at org.mybatis.spring.SqlSessionTemplate$SqlSessionInterceptor.invoke(SqlSessionTemplate.java:365)
	at com.sun.proxy.$Proxy11.selectOne(Unknown Source)
	at org.mybatis.spring.SqlSessionTemplate.selectOne(SqlSessionTemplate.java:160)
	at org.apache.ibatis.binding.MapperMethod.execute(MapperMethod.java:95)
	at org.apache.ibatis.binding.MapperProxy.invoke(MapperProxy.java:40)
```

在oracle中，sql语句中的in(xx,xx,xx,xx,......,xx,xx)字符串长度不能超过1000个，即范围查询不能大于1000
