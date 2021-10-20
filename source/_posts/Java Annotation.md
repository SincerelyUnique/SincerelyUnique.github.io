---
title: Java Annotation
date: 2015-09-11 13:51:34
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

JavaAnnotation


摘要：JDK5开始，java增加了对元数据（MEtaData）支持，一种类似于修饰符的工具，即Annotation，也即代码特殊标记，执行编译可读取并做出相应处理，多嵌入信息进行某些补充，且不影响程序代码的执行，访问和处理Annotation的工具统称APT（AnnotationProcessing Tool）。


基本Annotation：

java.lang包下


@Override

限定重写父类方法，指定方法覆載，保证重写，否则编译报错


@Deprecated

标记已过时，其他程序使用此标记程序类或方法时，弹出警告


@SuppressWarnings

抑制编译器警告，如屏蔽没有使用泛型所导致的编译器警告

eg：@suppressWarnings(value=”ubchecked”)


@SafeVarargs（JDK7）

“堆污染”警告，泛型擦除时代码可能运行时异常，而引发这种异常的原因就是“堆污染”，当把一个不 带泛型的对象赋值给一个带泛型的变量时，往往会发生这种“堆污染”，

有三种方法抑制这种警告：1.@SafeVarargs修饰引发该警告的方法或构造器

2.使用@SuppressWarnings修饰

3.使用-Xlint：varags选项（少用）


JDK的元Annotation

java.lang.annotation包下


@Retention

仅修饰一个Annotation定义，指定被修饰的Annotation可以保留多长时间，包含一成员变量 value，值有只三个： 1.RetentionPolicy.CLASS

2.RetentionPolicy.RUNTIME

3.RetentionPolicy.SOURCE


@Target

仅修饰一个Annotation定义，用于指定被修饰的Annotation能用于修饰哪些程序单元


@Documented

用于指定被该元Annotation修饰的Annotation类将被javadoc工具提取成文档


@Inherited

指定被他修饰的Annotation将具有继承性-如果某个类使用了@AAnnotation修饰，其子类自动被 @A修饰


自定义Annotation

```
public @interface Test{

...

}
```


