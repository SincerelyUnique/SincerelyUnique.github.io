---
title: soap The endpoint reference (EPR) for the Operation not found...
date: 2021-02-19 09:35:58
tags:
- soap
categories:
---

## 场景

postman执行soap post请求

## 异常

org.apache.axis2.AxisFault: The endpoint reference (EPR) for the Operation not found is /xxx/service and the WSA Action = null
org.apache.axis2.AxisFault: The endpoint reference (EPR) for the Operation not found is http://xxx.xxx.com/xxx/service and the WSA Action = null

## 参考

https://stackoverflow.com/questions/20651448/error-the-endpoint-reference-epr-for-the-operation-not-found

## 修改

Headers添加属性

```raw
Content-Type：text/xml   （注意不是application/xml）
SOAPAction：xxxx（具体你的action）
```
