---
title: Python获取存储过程结果集
date: 2022-01-25 14:34:43
tags:
- python
- procedure
categories:
- [学习, Python语言学习, Python]
---

> 记录python调用存储过程方法

# 创建测试表
```sql
create table TEST_TABLE
(
    CODE                       NUMBER      default NULL,
    TEXT                       VARCHAR2(20),
    CREATETIME                 DATE        default sysdate
);
```

# 录入数据
```sql
insert into TEST_TABLE (CODE, TEXT, CREATETIME) values (88, 'Jalen test88', sysdate-1);
insert into TEST_TABLE (CODE, TEXT, CREATETIME) values (888, 'Jalen test888', sysdate);
```

# 创建存储过程
```sql
CREATE or replace PROCEDURE stored_procedure_jalen_test(
    input_code in number, 
    res out sys_refcursor
)
as
begin
    open res for 
        select CODE, TEXT, CREATETIME from TEST_TABLE where code=input_code;
end;
```

# 查询存储过程
```sql
select text from user_source where name=upper('stored_procedure_jalen_test') order by line;
```

# 测试存储过程
> 这块需要进一步了解，因为下面的方法一我们没有开启，方法二我这边没有sqlplus
## 方法一
```sql
-- 需要设置set serveroutput on;
begin
    DBMS_OUTPUT.PUT_LINE('Hello World');
end;

DECLARE
  v_cur SYS_REFCURSOR;
  v_a   number;
  v_b   VARCHAR2(20);
  v_c   date;
BEGIN
  stored_procedure_jalen_test(88, v_cur);

  LOOP
    FETCH v_cur INTO v_a, v_b, v_c;
    EXIT WHEN v_cur%NOTFOUND;
    dbms_output.put_line(v_a || ' ' || v_b || ' ' || v_c);
  END LOOP;
  CLOSE v_cur;
END;
```

## 方法二
```sql
-- 需要从sqlplus执行
SQL> var rc refcursor
SQL> execute stored_procedure_jalen_test(88, :rc)
SQL> print rc
```

# 执行存储过程
```python
if __name__ == '__main__':
    logger.info('Run procedure and get data... ...')
    connection = engine.raw_connection()
    try:
        # 方法一
        # cursor = connection.cursor()
        # out_cursor = connection.cursor()
        # in_code = 88
        # cursor.callproc("stored_procedure_jalen_test", [in_code, out_cursor])
        # results = list(out_cursor.fetchall())
        # cursor.close()

        # 方法二
        # cursor = connection.cursor()
        # out_cursor = connection.cursor()
        # in_code = 88
        # test_results = cursor.callproc("stored_procedure_jalen_test", [in_code, out_cursor])
        # l_results = test_results[-1]
        # cols = [x[0] for x in l_results.description]
        # df = pd.DataFrame(list(l_results), columns=cols)

        # 方法三
        cursor = connection.cursor()
        out_cursor = connection.cursor()
        cursor.execute("begin stored_procedure_jalen_test(:1,:2); end;", [88, out_cursor])
        res = out_cursor.fetchall()

        # query with pandas... ... researching
    except Exception as e:
        logger.error('Get checked data for bov error: %s' % getattr(e, 'message', repr(e)))
        logger.error(traceback.format_exc())
    finally:
        connection.close()
    print('End!')
```

#参考

https://docs.oracle.com/javadb/10.8.3.0/ref/rrefcallprocedure.html

https://cx-oracle.readthedocs.io/en/latest/user_guide/plsql_execution.html

https://renenyffenegger.ch/notes/development/databases/Oracle/SQL/cursor/ref/sys_refcursor/index
