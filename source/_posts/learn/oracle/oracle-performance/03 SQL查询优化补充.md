---
title: (3) SQL查询优化（补）
date: 2020-08-20 17:42:33
categories:
- [学习, Oracle学习, Oracle性能优化]
tags:
- oracle
- sql
---

```
主要记录一下查看执行计划的方式，有些转自网上，仅供参考
 
1.set autotrace: 不实际执行，执行计划不一定准确， sqlplus查看
     usage： set autot[race] {off | on | trace[only]}
             [exp[pain]] [stat[istics]]
     （1）on 和 trace的区别是后者不产生sql的输出结果；
     （2）exp 和 stat前者只查看执行计划，后者只查看统计信息
 
2. explain plan for SQL：不实际执行，执行计划不一定准确
      explain plan set statement_id='definition_plan_t1' for select * from xxx where xxxx='xxxxx';
 
       select *
	 from plan_table
	start with id = 0 and statement_id = 'definition_plan_t1'
      connect by prior id = parent_id and statement_id = 'definition_plan_t1'
	order by id;
 
       select * from table(dbms_xplan.display(NULL , 'definition_plan_t1', 'ADVANCED'))
 
   执行计划查看推荐方法：dbms_xplan
   （1）display_cursor： 内存种shared_pool游标缓存
                  参数： sql_id、cursor_child_no、format
        select * from table(dbms_xplan.DISPLAY_CURSOR('&SQL_ID', null , 'ADVANCED ALLSTATS LAST'))
   （2）display_awr：awr基表wrh$_sql_plan
                  参数： sql_id、plan_hash_value、db_id、format
        select * from table(dbms_xplan.display_awr('&SQL_ID', null , null , 'ADVANCED +PEEKED_BINDS'))
   说明：
        （1）sql_id: 输入null则表示最后运行的一条sql语句，sql_id获取方法如下：
                     select sql_id, sql_text from v$sql where sql_text like 'select * from xxx%';
        （2）cursor_child_no： 输入null则表示显示所有子游标
        （3）format：ALLSTATS = IOSTATS + MEMSTATS
                     LAST 基于最后一次统计信息的执行计划
                     …… ……
             ALTER SESSION SET statistics_LEVEL = ALL;  --TYPICAL
             select * from table(dbms_xplan.display_cursor(null,null, 'ALLSTATS LAST')) 
             Operation：访问路径或链接方式
             Name：sql语句中的对象
             Starts：执行次数
             E-Rows：E-Bytes、E-Time：CBO基于现有统计信息评出来的值
             A-Rows：实际行数
             A-Time：累计时间
             Buffers：累计逻辑读次数
             Read：累计物理读次数
             Cost（%CPU）：Cost = IO Cost + CPU Cost -> v$sql_plan (评估值仅参考)
 
3. 10046或SQL Trace：实际执行，借助tkprof
   10046 trace帮助我们解析一条/多条SQL、PL/SQL语句的运行状态 ，这些状态包括 ：Parse/Fetch/Execute
   三个阶段中遇到的等待事件、消耗的物理和逻辑读、CPU时间、执行计划等等。
   
   oracle 11g中event++的特性允许我们只对某个特定的SQL收集10046 trace.（低版本是对所有sql收集）
   即在运行这条SQL时开启10046 trace,在这条SQL运行完之后关闭10046 trace.这样可以显著的降低生成的trace的大小。
 
   开启的步骤是(要把下面的awsh60c8mpfu1替换成那条SQL的SQL_ID)：
   alter system set events 'sql_trace [sql: awsh60c8mpfu1] level 12';
   而关闭的步骤是(要把下面的awsh60c8mpfu1替换成那条SQL的SQL_ID)：
   alter system set events 'sql_trace [sql: awsh60c8mpfu1] off';
 
   tkprof格式化后定位问题 
 
   -- 56912, 20080为10046跟踪sql执行过程的obj#
   select owner, object_name, object_id from dba_objects where object_id in (56912, 20080);
 
   10046 和SQL TRACE的区别？
   10046 比 SQL_TRACE参数提供更多的控制选项，更详细的内容输出，一般只用10046 而不用sql_trace
 
   10046 和10053 的区别？
   10053 是最常用的Oracle 优化器optimizer 跟踪trace， 10053 可以作为我们解析 优化器为什么
   选择某个执行计划，其中的理由的辅助工具，但并不告诉我们这个执行计划 到底运行地如何。
   而10046 并不解释 optimizer优化器的工作， 但它同样说明了在SQL解析parse阶段所遇到的等待事
   件和所消耗的CPU等资源，以及Execute执行和Fetch阶段的各项指标。
 
   分享一下10046的可视化工具：Mumbai  （我下载后被检测有毒。。。）
   来自：http://t.askmaclean.com/thread-2887-1-1.html
         https://www.marcusmoennig.de/blog/mumbai/
 
4. v$sql_plan：数据库种实际执行的sql
   执行顺序图解法：树形构图法，根据缩进来作图
   类似二叉树后序遍历法：
   （1）先遍历左子树
   （2）再遍历右子树
   （3）最后遍历根节点
   （4）左节点先于右节点执行
   （5）子节点先于父节点执行
   （6）相同缩进上下同父兄弟节点，兄先执行
  
   如：
   select sql_id, hash_value, id, parent_id, position, depth, operation, object_name, options 
     from v$sql_plan 
    where sql_id='2bsbpngwmhtrz'
   position：缩进相同，有同一个父节点，子节点的执行顺序
   parent_id：父节点id，子节点输出结果给父节点执行下一步
   depth：向右缩进层数或置于顶部的高度
 
   常用执行计划查看方法：
   （1）Current Execution Plans (last execution | all execution)
   select sql_id from v$sql where sql_text like 'select * from xxx%';
   select rpad('Inst: ' || v.inst_id, 9) || ' ' || rpad('Child: ' || v.child_number, 11) inst_child,
          t.plan_table_output
     from gv$sql v,
          table(dbms_xplan.display('gv$sql_plan_statistics_all',
                                   null,
                                   'ADVANCED ALLSTATS LAST -Projection -Outline -Note',
                                   'inst_id = ' || v.inst_id ||
                                   ' AND sql_id = ''' || v.sql_id ||
                                   ''' AND child_number = ' || v.child_number)) t
    where v.sql_id='2bsbpngwmhtrz' 
          --and v.executions > 1  --all execution
          and v.loaded_versions > 0;
   
   （2）Historical execution plans
    select t.plan_table_output
      from (select distinct sql_id, plan_hash_value, dbid
              from dba_hist_sql_plan
             where sql_id='2bsbpngwmhtrz') v,
           table(dbms_xplan.display_awr(v.sql_id, v.plan_hash_value, null, 'ADVANCED ALLSTATS')) t;
5. PL/SQL/SQL develop
```
