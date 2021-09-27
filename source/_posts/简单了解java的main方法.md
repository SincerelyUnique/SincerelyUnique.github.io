---
title: 简单了解java的main方法
date: 2020-10-15 19:34:46
tags:
- java
categories:
---

```
为什么执行java类会自动执行main方法？
 
这是一个很有趣的问题：
 
代码：下面两种main函数写法均正确
      public static void main(String[] args){}
      public static void main(String... args){}  （可变参数从jdk1.5开始）
 
分析点：public访问修饰符、static静态修饰符、void无返回参数、main函数名字、入参args，五方面
 
如果一个java类不包含main方法或者main方法书写不正确，执行结果如下
C:\Users\jalchu\Desktop> javac .\Test.java
C:\Users\jalchu\Desktop> java .\Test.class
Error: Could not find or load main class .\Test.class
C:\Users\jalchu\Desktop>
 
 
1. main方法为什么使用public访问修饰符？
public是一个访问修饰符，同样的修饰符还有private、protected，也可以不写任何访问修饰符。
 
private : 在同一类内可见。使用对象：变量、方法。 注意：不能修饰类（外部类）
public : 对所有类可见。使用对象：类、接口、变量、方法
protected : 对同一包内的类和所有子类可见。使用对象：变量、方法。 注意：不能修饰类（外部类）。
default (即默认，什么也不写）: 在同一包内可见，不使用任何修饰符。使用对象：类、接口、变量、方法。
访问修饰符作用是指定哪里或者谁可以访问这个方法，将main方法声明为public是为了是之全局可用（或可达），
只有这个类它是公开的，JVM才可以从类的外部调用它。
 
 
2. main方法为什么使用static静态修饰符？
首先了解什么是staic以及static的作用。
ref：https://www.edureka.co/blog/static-keyword-in-java/
（1）static是java中57个保留字（reserved words）之一；
     ref：https://en.wikipedia.org/wiki/List_of_Java_keywords
（2）static可以修饰Block|Variable|Method|Nested Classes；
（3）static关键字主要用于内存管理方面（节约内存，对象的频繁创建和销毁对jvm来说是有压力的），用于给定类的变量或方法的共享；
（4）static用于定义常量变量或定义对于类的每个实例都可用的相同名字的方法。
（5）static修饰的类成员变量/方法可以在创建其类的对象之前对其进行访问，无需先实例化对象后再引用。
     例如当我们从idea创建一个实例并通过实例调用该类的static变量时会产生警告（但不影响输出）；
（6）Static Block：静态代码块，仅在类第一次加载时执行一次，可用于初始化静态变量或初始化时对静态变量做一些操作
（7）Static Variable：静态变量，类级别对象共享，本质上静态变量就是全局变量，所有实例共享同一变量，
     静态变量只可以在类级别上创建；
（8）Static Methods：最突出的就是main方法，静态方法有以下特点：
     1. 他们只能直接调用其他静态方法
     2. 他们可以直接访问静态数据，但不能访问非静态数据；
     3. static方法内不能调用super，不能使用this
（9）Static Class：只有嵌套类才可以使用static修饰符，或称静态内部类，这种类不需要外部类的引用，
     一个静态类不能访问外部类的非静态成员；
main方法定义成static是为了使jvm可以直接加载而无需实例化，即无需先创建类的对象（jvm是c写的）。
 
如果main允许为非static，那么jvm在调用main方法前必须实例化其类，在实例化它时必须调用该类的构造函数，
如果该类的构造函数接受一个参数，假如我们的类有多个构造函数（如最常见的构造器重载），而其中一个构造
函数的入参也是String类型的数组或可变参数，将存在歧义，无法保证一致。
 
在java里面，static修饰的方法和属性是可以在不需要实例化的时候就可以去访问的，并且我们知道static
方法内部只可以使用该类static属性。
 
 
3. main方法为什么使用void无返回修饰符？
main方法不返回任何值是因为main方法结束后jvm中java进程也随之终结，完全没有必要进行多余的输出。
 
 
4. main方法为什么使用main作为函数名？
由于c/c++也具有类似的main方法，该方法用作程序执行的入口点，因此java也遵循该约定使用main作为入口。
 
 
5. main方法为什么使用String[] args或String... args作为参数？
首先明确一点，args这个名字可以是任意字符串，String[] aaa或String[] bbb都没问题，重点在于接收的参数类型。
一种是String[]，一种是String...
既然是参数，肯定会有一种传入方式，而这种传入方式就是当我们在使用java命令执行class文件时在class文件名字后面传入的后续数据
如：java Test.class a b c d
此时：for循环输出args就会得到a b c d
 
 
所以main方法声明为public，static，void，String[]或String...
 
 
 
 
 
补充：
main方法又称主函数，是编译器开始执行程序的起点，或者说是核心Java程序的入口点，区别于c/c++的main函数，
java的main函数使用void修饰不能返回值，而c可以返回int。
 
jvm可以搜索一个类里是否含有public static void main(String args[])字样的方法
 
按照程序运行的机制，一个程序的执行必然是由另一个更之前的程序去启动，最原始的就到了回变指令与操作系统。
写成main是因为编译器默认会去把他作为程序执行的入口（起点），这是约定俗成的一件事。
 
Java类中声明的所有内容都属于引用类型，并且需要在使用对象之前先创建对象，但是静态方法和静态数据将加载
到称为上下文的JVM内部的单独内存中，该内存是在类加载时创建。如果main方法是静态的，那么它将被加
载到JVM上下文中并可以执行。
 
Java虚拟机把描述类的数据从Class文件加载到内存，并对数据进行校验、转换解析和初始化，最终形成可以
被虚拟机直接使用的Java类型，这就是虚拟机的加载机制。*
Class文件由类装载器装载后，在JVM中将形成一份
描述Class结构的元信息对象，通过该元信息对象可以获知Class的结构信息：如构造函数，属性和方法等，Java
允许用户借由这个Class相关的元信息对象间接调用Class对象的功能,这里就是我们经常能见到的Class类。
 
 
类加载过程：加载 - 连接（验证-准备-解析）- 初始化 - 使用 - 卸载
加载：或称装载，即查找和导入Class文件，将十六进制的字节码文件读入内存，放入运行时数据区-方法区，加载
      方式很多，比如最常见的从jar包或war包加载；
连接：把类的二进制数据合并到JRE中；
      验证：检查载入Class文件数据的正确性
      准备：方法区中进行分配，给类的静态变量分配存储空间，不包括实例变量，实例变量会在对象实例化时
            随着对象一块分配在Java堆；
      解析：虚拟机将常量池内的符号引用转成直接引用；
初始化：类加载过程的最后一步，对类的静态变量，静态代码块执行初始化操作。
 
 
类加载器与双亲委派原则（Parent Delegation Model）：
      启动类加载器（Bootstrap ClassLoader）- JAVA_HOME\lib - jvm - 无法被java程序直接引用
      扩展类加载器（Extension ClassLoader）- JAVA_HOME\lib\ - jvm - 可被开发人员直接引用
      应用程序类加载器（Application ClassLoader）- Classpath\ - jvm - 直接使用/默认类加载器
该模型要求除了顶层的启动类加载器外，其余的类加载器都应当有自己的父类加载器。子类加载器和父类加载
器不是以继承（Inheritance）的关系来实现，而是通过组合（Composition）关系来复用父加载器的代码。
 
双亲委派模型的工作过程为：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，
而是把这个请求委派给父类加载器去完成，每一个层次的加载器都是如此，因此所有的类加载请求都会传给
顶层的启动类加载器，只有当父加载器反馈自己无法完成该加载请求（该加载器的搜索范围中没有找到对应
的类）时，子加载器才会尝试自己去加载。
 
 
 
java实例化对象的几种方式：
1. new一个对象
2. 工厂方法生产一个对象，String.valueOf(123); 
3. 反射一个对象，Class.forName("xxx").newInstance(); 
4. clone一个对象
5. 反序列化一个对象
 
 
 
 
参考：
https://www.tutorialspoint.com/why-the-main-method-in-java-is-always-static
https://javarevisited.blogspot.com/2011/12/main-public-static-java-void-method-why.html
https://juejin.im/post/6844903564804882445
https://blog.csdn.net/u014371184/article/details/78212895
https://www.geeksforgeeks.org/understanding-public-static-void-mainstring-args-in-java/
```
