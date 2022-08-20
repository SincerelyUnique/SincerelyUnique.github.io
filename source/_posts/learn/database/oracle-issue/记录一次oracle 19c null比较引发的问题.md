---
title: 记录一次oracle 19c null比较引发的问题
date: 2021-06-18 12:23:44
tags:
- oracle
categories:
- [学习, 数据库学习, Oracle问题记录]
---

# 记录一次oracle 19c null比较引发的问题

```sql
-- A='', B='', A和B会转为null，比较结果xxx：ccc, A既不等于B，也不不等于B， 处于不可比较的一种状态
with t1 as (select '' a, '' b from dual )
select t1.*, case when t1.a=t1.b then 'aaa' when t1.a!=t1.b then 'bbb' else 'ccc' end xxx from t1;
 
-- A='', B='1'， A会转为null， 比较结果xxx：ccc, 虽然B有值，但仍然处于不可比较的一种状态
with t1 as (select '' a, '1' b from dual )
select t1.*, case when t1.a=t1.b then 'aaa' when t1.a!=t1.b then 'bbb' else 'ccc' end xxx from t1;
 
-- A='', B=null, A会转为null， 比较结果xxx：ccc, 仍然处于不可比较的一种状态
with t1 as (select '' a, null b from dual )
select t1.*, case when t1.a=t1.b then 'aaa' when t1.a!=t1.b then 'bbb' else 'ccc' end xxx from t1;
 
-- A=null， B=null， 比较结果xxx：ccc, 仍然处于不可比较的一种状态
with t1 as (select null a, null b from dual )
select t1.*, case when t1.a=t1.b then 'aaa' when t1.a!=t1.b then 'bbb' else 'ccc' end xxx from t1;
 
-- 结论： oracle 的null 不可比较， 或许可以转换一下使用
 
with t1 as (select '' a, '' b from dual )
select t1.*, case when nvl(t1.a, '9999')=nvl(t1.b, '9999') then 'aaa'
                  when nvl(t1.a, '9999')!=nvl(t1.b, '9999') then 'bbb'
                  else 'ccc'
    end xxx from t1;
 
with t1 as (select '' a, '' b from dual )
select t1.*, case when decode(t1.a, '', '2')=decode(t1.b, '', '2') then 'aaa'
                  when decode(t1.a, '', '2')!=decode(t1.b, '', '2') then 'bbb' else 'ccc' end xxx from t1;
```

参考：
https://docs.oracle.com/cd/B19306_01/server.102/b14200/sql_elements005.htm

描述如下：

If you use any other condition with nulls and the result depends on the value of the null, then the result is UNKNOWN. Because null represents a lack of data, a null cannot be equal or unequal to any value or to another null. However, Oracle considers two nulls to be equal when evaluating a DECODE function. Please refer to DECODE for syntax and additional information.
