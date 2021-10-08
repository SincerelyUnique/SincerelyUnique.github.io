---
title: oracle拆分某个字段
date: 2018-05-10 15:45:36
tags:
- oracle
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

## 表

表：uf_wzlb （物资类别）

表：uf_wzzl （物资种类）

表：uf_wzlb_wzzl  （物资类别和物资种类关联表，物资类别与物资种类为一对多关系）

## 情景 

如，物资类别编码为

WZLB-201805100004

该物资类别绑定了多个物资种类，绑定的物资种类编码为

WZZL-201805100044,WZZL-201805100015,WZZL-201805100039,WZZL-201805100043,WZZL-201805100041,WZZL-201805100040,WZZL-201805100045,WZZL-201805100030

注意在末尾拼一个逗号，oracle拼接字符串用 “||”。

```sql
SELECT ID, MZ, BH
  FROM UF_WZZL
 WHERE BH IN (SELECT REGEXP_SUBSTR((SELECT wzzl
                                      FROM uf_wzlb_wzzl
                                     WHERE wzlb = 'WZLB-201805100002') || ',',
                                   '[^,]+',
                                   1,
                                   rownum)
                FROM dual
              CONNECT BY rownum <= LENGTH(REGEXP_REPLACE((SELECT wzzl
                                                            FROM uf_wzlb_wzzl
                                                           WHERE wzlb =
                                                                 'WZLB-201805100002') || ',',
                                                         '[^,]',
                                                         NULL)))
```

```sql
 SELECT ID, MZ, BH
   FROM UF_WZZL
  WHERE BH IN (SELECT REGEXP_SUBSTR('WZZL-201805100031,WZZL-201805100032,WZZL-201805100033,WZZL-201805100034,WZZL-201805100035,WZZL-201805100030,',
                                    '[^,]+',
                                    1,
                                    rownum)
                 FROM dual
               CONNECT BY rownum <= LENGTH(REGEXP_REPLACE('WZZL-201805100031,WZZL-201805100032,WZZL-201805100033,WZZL-201805100034,WZZL-201805100035,WZZL-201805100030,',
                                                          '[^,]',
                                                          NULL)))
```
