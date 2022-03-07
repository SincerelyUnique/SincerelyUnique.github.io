---
title: SQLAlchemy ORM oracle dblink usage
date: 2022-03-07 14:34:43
tags:
- sqlalchemy
- oracle
- dblink
categories:
---

可以参考我之前在github上提的discussion： https://github.com/sqlalchemy/sqlalchemy/discussions/7717

目前可以将代码修改成以下方式，将来也许会有更好的支持，感谢@zzzeek

```python
from sqlalchemy import Column
from sqlalchemy import DateTime
from sqlalchemy import MetaData
from sqlalchemy import String
from sqlalchemy import Table
from sqlalchemy.dialects import oracle
from sqlalchemy.sql.expression import quoted_name

option = Table(
    quoted_name("some_table@some_link", quote=False),
    MetaData(),
    Column("id", String(32)),
    Column("optionname", String(128)),
    Column("description", String(128)),
    Column("createtime", DateTime),
    Column("lastmodifiedtime", DateTime),
    Column("type", String(32)),
)


option_alias = option.alias("some_table")

stmt = option_alias.select().where(option_alias.columns.id == "xxx")

# to execute the statement
result = some_connection.execute(stmt)

# or to print it locally
print(stmt.compile(dialect=oracle.dialect()))
```

