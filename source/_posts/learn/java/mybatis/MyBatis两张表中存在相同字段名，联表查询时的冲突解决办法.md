---
title: MyBatis两张表中存在相同字段名，联表查询时的冲突解决办法
date: 2017-11-20 12:44:46
tags:
- mybatis
categories:
- [学习, Java语言学习, MyBatis]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

经常使用类似left join做查询，偶尔遇到两张表的字段名相同（即column名字一致），此时可以在mybatis中这样配置

例子：

```xml
    <select id="demo" resultMap="DemoResultMap">
        SELECT DISTINCT
               t1.username,
               t1.password,
 
               t2.username "t2_username",
               t2.age
          FROM table1 t1
          LEFT JOIN table2 t2
            ON t1.id = t2.id
         WHERE t1.age = #{age,jdbcType=VARCHAR}
    </select>
    <resultMap id="DemoResultMap" type="com.demo.vo.UserVo" >
        <id column="id"  property="id" jdbcType="VARCHAR"/>
        <result column="username" property="username" jdbcType="VARCHAR"/>
        <result column="password" property="password" jdbcType="VARCHAR"/>
        
        <result column="t2_username" property="t2Username" jdbcType="VARCHAR"/>
        <result column="age"  property="age" jdbcType="VARCHAR"/>
    </resultMap>
```

table1表和table2表都包含一个username字段，但实体中字段名是不同的，当联表查询时，可以起一个别名t2_username做转换，然后通过实体接收即可。

参考：https://www.cnblogs.com/hupi/p/5656742.html
