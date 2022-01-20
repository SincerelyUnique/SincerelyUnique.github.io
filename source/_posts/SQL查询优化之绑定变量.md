---
title: SQL查询优化之绑定变量
date: 2022-01-20 14:34:43
tags: 
- Python
- SQLAlchemy
- sql
- oracle
categories:
---

# SQL绑定参数

我们日常写sql查询语句最常用的有两种方式，一种是raw sql（替换变量或文字编写SQL语句），一种是bind variable，两种区别很大。通常使用绑定变量可以最大限度地减少数据库服务器处理时间，并且可以将应用程序性能提高20%到30%。同时可以避免sql注入风险。

## 检测top sql
我们可以通过下面sql查询当前oracle服务器top sql，通常这些top sql都没有使用绑定变量方式，并且这些sql每次oracle cbo都要重复解析，所以会占用很大的内存资源，拖慢了oracle运行速度。下面是查询内存中当前cache的全局查询语句（检测执行频率为1000以上）。

```sql
select *
from (
         select substr(sql_text, 1, 100), count(*) numbers
         from gv$sql
         group by substr(sql_text, 1, 100)
         having count(*) > 1000
     )
order by numbers desc
```

> gv@sql: The view you are using gets the information from the shared sql area of the shared pool. This is SQL Oracle caches to avoid parsing queries that are the same as others recently parsed. How long text gets retained depends on several factors among which are space available, how recently the text was reused, and the volume of SQL parsed. This means the contents of the sql shared area are dynamic and may not retain certain text at different times during the day.

## 使用
python可以使用冒号（:）进行变量绑定，对于使用绑定变量这种方式的查询，gv$sql只会产生1条解析记录。比如insert into table_user(name, age) values('jalen', '30')如果绑定变量，那么这个query在数据库里看起来就会大致变成这样子：insert into table_user values(name, age) values(:1, :2)。

下面针对一些特殊查询，比如like查询，in查询和between查询，列举一些python orm中可以使用的绑定变量方式。

```
from datetime import datetime

import pandas as pd
from sqlalchemy import text, create_engine, bindparam, DateTime
from sqlalchemy.pool import NullPool

from config import NewConfig
engine = create_engine('xxx', poolclass=NullPool, echo=True)


def like_query_test():
    text1 = 'jalen'
    sql1 = "select * from table_test where TEXT like :text || '%'"
    df1 = pd.read_sql_query(sql1, engine, params=[text1])
    print(df1)

    text2 = 'query1'
    sql2 = "select * from table_test where TEXT like '%' || :text"
    df2 = pd.read_sql_query(sql2, engine, params=[text2])
    print(df2)

    text3 = 'test'
    sql3 = "select * from table_test where TEXT like '%' || :text || '%'"
    df3 = pd.read_sql_query(sql3, engine, params=[text3])
    print(df3)

    text1 = 'jalen'
    sql1 = "select * from table_test where TEXT like :text || '%'"
    res1 = engine.execute(text(sql1), text=text1).first()
    res1 = engine.execute(text(sql1), text=text1).fetchall()
    print(res1)

    text2 = 'query1'
    sql2 = "select * from table_test where TEXT like '%' || :text"
    res2 = engine.execute(text(sql2), text=text2).first()
    res2 = engine.execute(text(sql2), text=text2).fetchall()
    print(res2)

    text3 = 'test'
    sql3 = "select * from table_test where TEXT like '%' || :text || '%'"
    res3 = engine.execute(text(sql3), text=text3).first()
    res3 = engine.execute(text(sql3), text=text3).fetchall()
    print(res3)


def in_query_test():
    sql = text('SELECT CODE, TEXT, CREATETIME FROM table_test WHERE code IN :code_list')
    sql = sql.bindparams(bindparam('code_list', expanding=True))
    res = engine.execute(sql, code_list=[88, 1, 2]).fetchall()
    df = pd.read_sql_query(sql, engine, params={'code_list': [88, 1, 2]})
    print(res)
    print(df)


def between_query_test():
    from_date = datetime.strptime('2021-09-10 06:09:11', '%Y-%m-%d %H:%M:%S')
    to_date = datetime.strptime('2022-01-13 09:22:36', '%Y-%m-%d %H:%M:%S')
    sql = "select CODE, TEXT, CREATETIME " \
          "  from table_test " \
          " where CREATETIME between :from_date and :to_date"
    res = engine.execute(text(sql), from_date=from_date, to_date=to_date).fetchall()
    df = pd.read_sql_query(sql, engine, params=[from_date, to_date])
    df = pd.read_sql_query(sql, engine, params={'from_date': from_date, 'to_date': to_date})

    from_date = '2021-09-10 06:09:11'
    to_date = '2022-01-13 09:22:36'
    sql = "select CODE, TEXT, CREATETIME " \
          "  from table_test " \
          " where CREATETIME between " \
          "         to_date(:from_date, 'YYYY-MM-DD hh24:mi:ss') and " \
          "         to_date(:to_date, 'YYYY-MM-DD hh24:mi:ss')"
    res = engine.execute(text(sql), from_date=from_date, to_date=to_date).fetchall()
    df = pd.read_sql_query(sql, engine, params=[from_date, to_date])

    from_date = datetime.strptime('2021-09-10 06:09:12', '%Y-%m-%d %H:%M:%S')
    to_date = datetime.strptime('2022-01-13 09:22:35', '%Y-%m-%d %H:%M:%S')
    sql = "select CODE, TEXT, CREATETIME " \
          "  from table_test " \
          " where CREATETIME >= :from_date " \
          "   and CREATETIME <= :to_date"
    res = engine.execute(text(sql), from_date=from_date, to_date=to_date).fetchall()
    df = pd.read_sql_query(sql, engine, params=[from_date, to_date])

    from_date = datetime.strptime('2021-09-10 06:09:12', '%Y-%m-%d %H:%M:%S')
    to_date = datetime.strptime('2022-01-13 09:22:35', '%Y-%m-%d %H:%M:%S')
    sql = "select CODE, TEXT, CREATETIME " \
          "  from table_test " \
          " where CREATETIME >= :from_date " \
          "   and CREATETIME <= :to_date"
    sql = text(sql).bindparams(bindparam('from_date', from_date, DateTime), bindparam('to_date', to_date, DateTime))
    res = engine.execute(sql).fetchall()
    df = pd.read_sql_query(sql, engine)


if __name__ == '__main__':
    like_query_test()
    in_query_test()
    between_query_test()
    print('End!')
```

## sql注入测试
并且使用绑定变量方式能够有效避免sql注入。比如下面的注入例子。

```python
def pandas_sql_inject_test():
    inject_text = "1' or 1=1 --"

    # 替换变量，导致sql注入，将全表数据返回
    sql = "select CODE, TEXT, CREATETIME from table_test where text='{0}'".format(inject_text)
    df = pd.read_sql_query(sql, engine)

    sql = "select CODE, TEXT, CREATETIME from table_test where text='%s'" % inject_text
    df = pd.read_sql_query(sql, engine)

    # 使用DB-API规范的绑定变量形式，有效避免了sql注入风险
    sql = "select CODE, TEXT, CREATETIME from table_test where text=:text"
    df = pd.read_sql_query(sql, engine, params=[inject_text])

    sql = "select CODE, TEXT, CREATETIME from table_test where text=:text"
    df = pd.read_sql_query(text(sql), engine, params={'text': inject_text})
    print(df)
```
