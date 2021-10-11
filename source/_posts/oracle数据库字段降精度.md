---
title: oracle数据库字段降精度
date: 2018-01-10 11:29:07
tags:
- oracle
- sql
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

说明：以合同评审表中的合同金额字段为修改对象，原金额类型是以万元为单位的number(16,6)，现需要降低精度到小数点后两位，即修改后以元为单位，为number(16,2)，执行操作如下

表名字： tbl_requisition_contract

列名字： contract_amount

```sql
/*修改原字段名name为name_tmp*/
alter table tbl_requisition_contract rename column contract_amount to contract_amount_tmp;
 
/*增加一个和原字段名同名的字段name*/
alter table tbl_requisition_contract add contract_amount NUMBER(16,2);
comment on column tbl_requisition_contract.contract_amount is '合同金额，单位元，保留两位小数 ';
 
/*将原字段name_tmp数据更新到增加的字段name，这边乘以10000，是万元转成元的意思*/
update tbl_requisition_contract set contract_amount=trim(contract_amount_tmp*10000);
 
/*更新完，删除原字段name_tmp*/
alter table tbl_requisition_contract drop column contract_amount_tmp;
```
