---
title: JPA结合Hibernate定义主键
date: 2017-10-13 14:53:31
tags:
- jpa
- hibernate
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

实际工作中遇到类似这样的问题，问题如下面StackOverflow中描述的

https://stackoverflow.com/questions/43424203/org-hibernate-annotationexception-unknown-id-generator-id-generator

# 下面是更改过后的代码（员工信息表对应的实体类），注意id的定义：

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.GenericGenerator;
 
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
 
/**
 * <p>
 * <code>Employee</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/13 14:54
 */
@Entity
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Employee {
 
    @GenericGenerator(name = "employeeSeqGenerator",
            strategy = "org.hibernate.id.enhanced.SequenceStyleGenerator",
            parameters = {
                    @org.hibernate.annotations.Parameter(name = "sequence_name", value = "EMPLOYEE_SEQUENCE"),
                    @org.hibernate.annotations.Parameter(name = "initial_value", value = "1000"),
                    @org.hibernate.annotations.Parameter(name = "increment_size", value = "1")
            })
    @Id
    @GeneratedValue(generator = "employeeSeqGenerator")
    private Long id;
 
    private String employeeName;
 
    //... ...
}
```

# 注解说明

1. @Id注解：JPA提供

顾名思义，该属性字段定义为主键，注意在源码注释中有讲只有下面几种类型才可以定义为主键

```java
import java.lang.annotation.Retention;
import java.lang.annotation.Target;
 
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.RetentionPolicy.RUNTIME;
 
/**
 * Specifies the primary key of an entity.
 * The field or property to which the <code>Id</code> annotation is applied
 * should be one of the following types: any Java primitive type;
 * any primitive wrapper type;
 * <code>String</code>;
 * <code>java.util.Date</code>;
 * <code>java.sql.Date</code>;
 * <code>java.math.BigDecimal</code>;
 * <code>java.math.BigInteger</code>.
 *
 * <p>The mapped column for the primary key of the entity is assumed
 * to be the primary key of the primary table. If no <code>Column</code> annotation
 * is specified, the primary key column name is assumed to be the name
 * of the primary key property or field.
 *
 * <pre>
 *   Example:
 *
 *   @Id
 *   public Long getId() { return id; }
 * </pre>
 *
 * @see Column
 * @see GeneratedValue
 *
 * @since Java Persistence 1.0
 */
@Target({METHOD, FIELD})
@Retention(RUNTIME)
 
public @interface Id {
}
```

2. @GeneratedValue注解：JPA提供

文档参考：http://docs.oracle.com/javaee/5/api/javax/persistence/GeneratedValue.html

源码定义如下：

```java
package javax.persistence;
 
import java.lang.annotation.Retention;
import java.lang.annotation.Target;
 
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.RetentionPolicy.RUNTIME;
import static javax.persistence.GenerationType.AUTO;
 
/**
 * Provides for the specification of generation strategies for the
 * values of primary keys.
 *
 * <p> The <code>GeneratedValue</code> annotation
 * may be applied to a primary key property or field of an entity or
 * mapped superclass in conjunction with the {@link Id} annotation.
 * The use of the <code>GeneratedValue</code> annotation is only
 * required to be supported for simple primary keys.  Use of the
 * <code>GeneratedValue</code> annotation is not supported for derived
 * primary keys.
 *
 * <pre>
 *
 *     Example 1:
 *
 *     @Id
 *     @GeneratedValue(strategy=SEQUENCE, generator="CUST_SEQ")
 *     @Column(name="CUST_ID")
 *     public Long getId() { return id; }
 *
 *     Example 2:
 *
 *     @Id
 *     @GeneratedValue(strategy=TABLE, generator="CUST_GEN")
 *     @Column(name="CUST_ID")
 *     Long id;
 * </pre>
 *
 * @see Id
 * @see TableGenerator
 * @see SequenceGenerator
 *
 * @since Java Persistence 1.0
 */
@Target({METHOD, FIELD})
@Retention(RUNTIME)
 
public @interface GeneratedValue {
 
    /**
     * (Optional) The primary key generation strategy
     * that the persistence provider must use to
     * generate the annotated entity primary key.
     */
    GenerationType strategy() default AUTO;
 
    /**
     * (Optional) The name of the primary key generator
     * to use as specified in the {@link SequenceGenerator}
     * or {@link TableGenerator} annotation.
     * <p> Defaults to the id generator supplied by persistence provider.
     */
    String generator() default "";
}
```

如上，包含两个属性，GenerationType和generator，其中GenerationType默认是AUTO

GenerationType几个属性的定义可以查看文档：http://docs.oracle.com/javaee/5/api/javax/persistence/GenerationType.html

generator自己定义

```
四种数据库的对GeneratorValue4种策略的支持情况如下：来自http://blog.csdn.net/tianxiezuomaikong/article/details/64930151
 
mysql 
GenerationType.TABLE 
GenerationType.AUTO 
GenerationType.IDENTITY 
不支持GenerationType.SEQUENCE
 
oracle 
strategy=GenerationType.AUTO 
GenerationType.SEQUENCE 
GenerationType.TABLE 
不支持GenerationType.IDENTITY
 
postgreSQL 
GenerationType.TABLE 
GenerationType.AUTO 
GenerationType.IDENTITY 
GenerationType.SEQUENCE 
都支持
 
kingbase 
GenerationType.TABLE 
GenerationType.SEQUENCE 
GenerationType.IDENTITY 
GenerationType.AUTO 
都支持
```

3. @GenericGenerator注解：Hibernate提供

源码：

```java
package org.hibernate.annotations;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;
 
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.ElementType.PACKAGE;
import static java.lang.annotation.ElementType.TYPE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;
 
/**
 * Generator annotation describing any kind of Hibernate generator in a generic (de-typed) manner.
 *
 * @author Emmanuel Bernard
 */
@Target({PACKAGE, TYPE, METHOD, FIELD})
@Retention(RUNTIME)
public @interface GenericGenerator {
	/**
	 * unique generator name.
	 */
	String name();
	/**
	 * Generator strategy either a predefined Hibernate strategy or a fully qualified class name.
	 */
	String strategy();
	/**
	 * Optional generator parameters.
	 */
	Parameter[] parameters() default {};
}
```

参考文档：

http://docs.jboss.org/hibernate/annotations/3.4/api/org/hibernate/annotations/GenericGenerator.html

http://blog.csdn.net/tianxiezuomaikong/article/details/64930151  13种策略

