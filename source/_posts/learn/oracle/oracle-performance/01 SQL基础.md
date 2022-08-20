---
title: (1) SQL基础
date: 2020-08-21 12:42:23
categories:
- [学习, 数据库学习, Oracle性能优化]
tags:
- oracle
- sql
---

```
SQL基础
（部分转自网络，仅供参考）
1. 关系型和非关系型数据库，如oracle和redis
  （1）查看oracle版本：
   select * from v$version;
   select version from v$instance;
   select version FROM Product_component_version   Where SUBSTR(PRODUCT,1,6)='Oracle';
 
  （2） 查看db时区：
   SELECT dbtimezone FROM DUAL;
   SELECT sessiontimezone FROM DUAL;
 
  （3）查看db编码：
   select * from nls_database_parameters where parameter ='NLS_CHARACTERSET';
   select * from nls_instance_parameters where parameter='NLS_LANGUAGE';
   select sys_context('userenv','language') from dual; 
 
  （4）查看所有db link：
   select * from dba_db_links;
 
  （5）查看当前用户的表
   select table_name from user_tables;
 
  （6）查看所有用户的表
   select table_name from all_tables;
 
  （7）查询所有用户
   select * from all_users;
   select * from dba_users;
 
  （8）查看当前连接会话
   select * from v$Session
 
  （9）查看当前用户权限
   select * from session_privs
 
  （10）dual表（或称伪表、假表、虚拟表，mysql也有）
   Oracle提供的最小的表，不论进行何种操作（不要删除记录），它都只有一条记录——'X'（select * from dual）。
   dual同时是sys用户下的一张内部表，所有用户都可以使用DUAL名称访问，无论什么时候这个表总是存在。
   它只有一列：DUMMY，其数据类型为：VARCHAR2(1)
   select owner, object_name , object_type from dba_objects where object_name like '%DUAL%';
   用途：
   1. select计算常量表达式、伪列等值
   2. 查看当前用户
      select user from dual;
      select count(*) from dual;
   3. 用做计算器
      select 7*9*10-10 from dual;
   4. 调用系统函数
      select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') from dual;  --获得当前系统时间
      select sys_context('userenv','terminal') from dual;  --获得主机名
      select sys_context('userenv','language') from dual;  --获得当前locale
      select DBMS_RANDOM.random from dual;  --获得一个随机数
   5. 查看序列值
      create sequence aaa increment by 1 start with 1;
      select aaa.nextval from dual;
      select aaa.currval from dual;
 
2. 数据操纵语言DML：select、insert、update、delete、merge
   数据定义语言DDL：create、alter、drop、truncate
   事务控制语言TCL：commit、rollback、savepoint
   数据控制语言DCL：grant、revoke
 
3. 数据类型：
   char：固定字符，最长2000个
   varchar2：可变长，最长4000最小1
   number：长度范围1~38，可以存整数或浮点数，
           注意number(m,n)[m为精度，n为小数位数，所以整数为m-n位]
           如：
           number(8)存储总长度为8的整数
           number(8,1)存储小数位为1位，总长度为8的浮点数，如果小数位数不足，则用0补全
   int：oracle本来就没有int类型，为了与别的数据库兼容，新增了int类型作为number类型的子集，
        int类型在创建后会转为number类型，int相当于number(22),存储总长度为22的整数。
        int类型可以存放时间戳，时间戳转时间：
	SELECT to_char('1598011200000' / (1000 * 60 * 60 * 24) +
               	       TO_DATE('1970-01-01 08:00:00', 'YYYY-MM-DD HH:MI:SS'),
                      'YYYY-MM-DD HH:MI:SS') AS CDATE
  	  FROM dual;
        SELECT to_date(to_char('1598011200000' / (1000 * 60 * 60 * 24) +
               	               TO_DATE('1970-01-01 08:00:00', 'YYYY-MM-DD HH:MI:SS'),
               		      'YYYY-MM-DD HH:MI:SS'), 'YYYY-MM-DD hh24:mi:ss') AS CDATE
  	  FROM dual;
   date：粒度秒，
        select sysdate from dual;
        select to_date('2020-8-28 03:12:00','yyyy-mm-dd hh24:mi:ss')-sysdate from dual;
	select to_date(to_char(systimestamp,'yyyy-mm-dd hh24:mi:ss'),'yyyy-mm-dd hh24:mi:ss') from dual;
	
	NLS_DATE_FORMAT参数用于设置日期显示格式
        select * from nls_session_parameters where parameter = 'NLS_DATE_FORMAT';
        select * from nls_instance_parameters where parameter = 'NLS_DATE_FORMAT';
        select * from nls_database_parameters where parameter = 'NLS_DATE_FORMAT';
	select * from nls_session_parameters where parameter='NLS_DATE_LANGUAGE';
	select * from nls_instance_parameters where parameter='NLS_DATE_LANGUAGE';
	select * from nls_database_parameters where parameter ='NLS_DATE_LANGUAGE';
       （session级别覆盖instance级别，instance级别覆盖database级别）
   timestamp：粒度更细，可带时区（TIMESTAMP WITH TIME ZONE）
	select systimestamp from dual;
	select localtimestamp from dual;
	select current_timestamp from dual; （默认当前session的time zone，同localtimestamp）
	select to_timestamp('2020-8-28 03:12:00','yyyy-mm-dd hh24:mi:ss')-systimestamp from dual;
	select to_timestamp(to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),'yyyy-mm-dd hh24:mi:ss') from dual;
	SELECT FROM_TZ(CAST(TO_DATE('1999-12-01 11:00:00', 'YYYY-MM-DD HH:MI:SS') AS TIMESTAMP),
                           'America/New_York')
               AT TIME ZONE 'America/Los_Angeles' "West Coast Time"
          FROM DUAL;
        timestamp以及时区可参考：https://docs.oracle.com/cd/B19306_01/server.102/b14225/ch4datetime.htm
   clob：character Large Object-字符大对象，存储单字节数据，文本数据
   blob：binary large object-二进制大对象，存储二进制文件数据。如一张图片或一个声音文件，保存位图等
 
4. 测试number、int、numeric、integer类型默认的长度
   ALTER TABLE STAP_PROV_TEST ADD default_int_len int NULL;
   ALTER TABLE STAP_PROV_TEST ADD default_numeric_len numeric NULL;
   ALTER TABLE STAP_PROV_TEST ADD default_number_len number NULL;
   ALTER TABLE STAP_PROV_TEST ADD default_number_len2 number(*) NULL;
   ALTER TABLE STAP_PROV_TEST ADD default_integer_len integer NULL;
 
   SELECT distinct b.column_name, --字段名
          b.data_type,            --字段类型
          b.data_length,          --字段长度
          a.comments              --字段注释
     FROM user_col_comments a,
          all_tab_columns b
    WHERE a.table_name = b.table_name
          and a.table_name = 'STAP_PROV_TEST';
 
   DEFAULT_INT_LEN,NUMBER,22
   DEFAULT_NUMERIC_LEN,NUMBER,22
   DEFAULT_NUMBER_LEN,NUMBER,22
   DEFAULT_NUMBER_LEN2,NUMBER,22
   DEFAULT_INTEGER_LEN,NUMBER,22
 
5. oracle decode函数和case声明
   decode：
   参考：https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions040.htm
   select decode(expr, 'search1', 'result1', 'search2', 'result2', ..., default) from table_xxx;
   select decode('animal', 'cat', '猫', 'dog', '狗', 'chick', '鸡', '其他动物') from table_animal;
   如果没有default，默认default为null。
   decode里有长度限制，最大为255。（oracle很多函数的参数都有长度限制，比如regexp_like正则表达式长度<512byte）
 
   case：
   参考：https://docs.oracle.com/cd/B19306_01/server.102/b14200/expressions004.htm
   case when animal='cat' then '猫'
        when animal='dog' then '狗'
        when animal='chick' then '鸡'
        else '其他动物'
        end 'animal'
 
   区别：
   1. case是statement声明，decode是函数
   2. case的逻辑操作不仅仅只是等于判断，而decode只能做等值判断
   3. case可以做逻辑比较，如<、>、between、like等等
   4. case可以跟谓词如in，case when salary in (9000, 10000) then '9K-10K'
   5. case可以跟子查询如，case when emp_no in (select mgr_no from dept) then 'dept_mgr'
   6. case可以用于PL/SQL construct，而decode只能作为一个函数应用在sql内部
   7. case可以作为function/procedure参数使用，decode不能
      exec myproc(case: A when 'three' then 3 else 0 end);  --right
      exec myproc(decode(:a, 'three', 3, 0));  --error
   8. case对类型敏感，类型不同会报错，而decode有类型转换
      select decode(200, 100, 100, '200', '200', '300') from dual;
      select case 200 when 100 then 100
                      when '200' then '200'
                      else '300'
             end test
        from dual;
   9. 两者对null处理输出不同
      select decode(null, null, 'this is null', 'this is not null') from dual;
      select case null when null then 'this is null'
                       else 'this is not null'
             end test
        from dual;
   10. 最重要一点：case执行速度比decode更优秀
```
