---
title: MYSQ - ERROR 1146 (42S02) - Table ‘xxx‘ doesn‘t exist一种情况
date: 2021-06-10 13:24:01
tags:
- mysql
categories:
- [学习, 数据库学习, MySQL]
---

# MYSQL：ERROR 1146 (42S02): Table ‘xxx‘ doesn‘t exist一种情况

原因：linux下表名大小写问题造成

MySQL在Linux下数据库名、表名、列名、别名大小写规则是这样的： 

1. 数据库名与表名是严格区分大小写的；
2. 表的别名是严格区分大小写的；
3. 列名与列的别名在所有的情况下均是忽略大小写的；
4. 变量名也是严格区分大小写的

```sql
show global variables like '%lower_case%';
 
+------------------------+-------+
| Variable_name          | Value |
+------------------------+-------+
| lower_case_file_system | OFF   |
| lower_case_table_names | 0     |
+------------------------+-------+
```

（1）lower_case_file_system

表示当前系统文件是否大小写敏感，只读参数，无法修改。
ON  大小写不敏感
OFF 大小写敏感
 

（2）lower_case_table_names

表示表名是否大小写敏感，可以修改。
lower_case_table_names = 0时，mysql会根据表名直接操作，大小写敏感。
lower_case_table_names = 1时，mysql会先把表名转为小写，再执行操作。

参考：

https://www.huaweicloud.com/articles/12ad8ab7f5df62e809301bcbf9078a1b.html
https://blog.csdn.net/fdipzone/article/details/73692929
