---
title: mybatis关联映射
date: 2017-08-16 08:58:04
tags:
- mybatis
categories:
- [学习, Java语言学习, MyBatis]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

场景：以学生和班级为例，一个学生只有一个班级，一个班级包含多个学生

说明：mapper标签中我没有添加namespace

1. StudentMapper.xml

```xml
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper>
    <resultMap id="studentResultMap" type="com.demo.domain.Student">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="sex" column="sex"/>
        <result property="age" column="age"/>
 
        <!--关联映射：一个学生关联一个班级-->
        <association property="clazz" column="clazz_id" javaType="com.demo.domain.Clazz" select="selectClazzWithId"/>
    </resultMap>
 
    <!--查询关联班级-->
    <select id="selectClazzWithId" resultType="com.demo.domain.Clazz">
        SELECT * FROM TB_CLAZZ WHERE id = #{id}
    </select>
 
    <!--查询学生-->
    <select id="selectStudent" resultMap="studentResultMap">
        SELECT * FROM TB_STUDENT
    </select>
</mapper>
```

2. ClazzMapper.xml

```xml
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper>
    <resultMap id="clazzResultMap" type="com.demo.domain.Clazz">
        <id property="id" column="id"/>
        <result property="code" column="code"/>
 
        <!--关联映射：一个班级有多个学生，返回一个List集合-->
        <collection property="students" javaType="ArrayList" column="id" ofType="com.demo.domain.Student" select="selectStudentWithId"/>
    </resultMap>
 
    <!--查询关联的学生-->
    <select id="selectStudentWithId" resultType="com.demo.domain.Student">
        SELECT * FROM TB_STUDENT WHERE clazz_id = #{id}
    </select>
 
    <!--查询班级-->
    <select id="selectClazz" resultMap="clazzResultMap">
        SELECT * FROM TB_CLAZZ
    </select>
</mapper>
```


