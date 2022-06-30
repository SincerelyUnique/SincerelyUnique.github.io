---
title: (1) System 类
date: 2022-05-31 16:05:49
categories:
- [学习, Java语言学习, 一天一个Java类]
tags:
- java
---

# 缘由

今天是第一天，本来想着每天能够学习一点东西，但是最近一会儿弄这个，一会弄那个，感觉时间很少，或者说是自己比较慵懒吧。

从很早的时候就想着某一天能够把jdk里面的所有java类搞明白，但是到头来还是没有做到。即便是日常常用的一些java类的实现细节可能也不清晰。

要么就是某些时候为了应对面试问题，奋笔疾书，翻看一些常用的集合类，一些常用的并发类等等。

希望能够从今天开始，每天了解一个java类，不需要特意背记，只求熟悉。

# 说明

这里统一用windows版本的.exe安装程序，jdk目前先下载以下几个版本：jdk7, jdk8, jdk9(主要是看String类的变化，也是为了找一个非LTS版本参照), jdk11, jdk17，

下载jdk

- jdk7：https://www.oracle.com/java/technologies/javase/javase7-archive-downloads.html
- jdk8: https://www.oracle.com/in/java/technologies/javase/javase8-archive-downloads.html
- jdk9: https://www.oracle.com/java/technologies/javase/javase9-archive-downloads.html  (jdk9下载的时候发现只有x64版本, 但是有300M+)
- jdk10: https://www.oracle.com/java/technologies/java-archive-javase10-downloads.html
- jdk11: https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html （100M+， 区分x86和x64，可能是因为LTS版本，更规范些）
- jdk12：https://www.oracle.com/java/technologies/javase/jdk12-archive-downloads.html （windows和jdk9一样只有x64版本）
- jdk17: https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html 

上面所有版本中7，8，11，17是LTS版本，主要看这些。

有时候我们会遇到native修饰的方法，这

# System.java

> From JDK1.0
> Package：java.lang

1. System类被final修饰，不可继承

2. System类拥有1个私有构造方法`private System() {}`，不可实例化

3. System类拥有1个静态初始化块`static { registerNatives(); }`，类初始化后调用native修饰的registerNatives方法。

4. registerNatives方法是clinit（注意init是实例化过程中对non-static变量解析初始化，clinit是jvm进行类加载-验证-解析-初始化过程中的初始化，有本质区别）过程中jvm做的第一件事，处于非常早的一个时间点。

5. 类初始化过程中，静态代码块中的父类的静态变量优先于子类进行赋值操作。

6. System类static块在1.7和1.8时调用registerNatives本地方法后，在类初始化与线程初始化后，会首先调用System类的initializeSystemClass私有方法，该方法调用native initProperties方法注入系统属性、调用`sun.misc.VM.saveAndRemoveProperties(props)`加载虚拟机参数（如最大直接内存、IntegerCache size等），并且加载zip类库，设置HUP、TERM、INT信号处理，初始化系统环境变量，因为主线程不会自动将自己添加到线程组，这里使用`current.getThreadGroup().add(current);`主动将自己添加进去。然后执行`setJavaLangAccess`，最后调用`sun.misc.VM.booted();`

7. 上面的registerNatives在java11和java17中，调用的是initPhase1方法，其实initPhase1和initializeSystemClass方法没太大区别，内部代码执行次序有些变化，调用了`VM.initLevel(1)`设置了虚拟机初始化级别。

8. 通常我们使用的System.out对象，其实是一个PrintStream对象，PrintStream对象里对println方法和print方法做了重载，支持了各种基本数据类型以及对象、char数组的输出。

10. 与System.out相对应，对于所有输入（如键盘），可以使用System.in，in是一个InputStream对象，可以通过InputStreamReader读取并通过BufferedReader的readLine方法获取到对应的输入信息。
```java
    public static void main(String[] args) {
        InputStreamReader isr = new InputStreamReader(System.in);
        BufferedReader br = new BufferedReader(isr);
        System.out.println("Please input your name: ");
        try {
            String name = br.readLine();
            System.out.println("Hello " + name);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

11. 注意out和in对象都是静态final的。

12. System类有一个被static volatile修饰的SecurityManager对象，其实不仅System类有用到，其他比如Proxy、Thread等类也用到了该类对象，该对象主要作用监控恶意程序（比如删除系统文件，reboot系统等），所以对执行的code做个权限的管理，防止意外。

13. System类还有一个err对象，类型和in对象一样是PrintStream，


# 英文单词

```
determine 决定
performing 表演，展示
possibly 可能
various 各种各样的
potentially 潜在的
invocation 调用
thereby 从而
prevent 防止
permitted 允许
convention 公约，惯例
obtain 获得
in addation to 此外
asterisk 星号
remain 保持
backwards 向后
compatible 兼容的
either 任何一个
restrict 受限制的
comma-separated 逗号分割
```


# 关于native方法

> A native method is a Java method whose implementation is provided by non-java code.

除了System类，其实java里面还有很多类比如Thread类、Class类、ClassLoader类、Object类、Unsafe类等都会在类初始化时通过静态代码块执行registerNatives方法，registerNatives方法（或者说native方法）对应的底层实现其实是用c/c++实现的，为了与其他语言通信，java里面提供了一个可以与其他语言之间通信的API，名字叫JNI，也就是Java Native Interface，实现它可以使java调用其他语言的函数。因为本地方法没有方法体，所以更像是定义了一个接口，而具体实现是通过其他语言实现。

native static修饰的方法不需要实例化这个类，可以直接调用，使用上非常方便，比如：
> native static public long Native2() ;

native方法还可以跟synchronized修饰，保证同步调用，比如：
> native synchronized private float Native3( Object o ) ;

native方法可以返回各种类型数据，可以是基本类型，也可以是引用类型，比如boolean, int或者是某个对象类型。

native方法还可以加入异常控制，比如抛出某个异常，同时也可以接收一些java类型参数（但是这样很麻烦，毕竟你想想，既然我们决定注入了这么多java特性，我们又为何不直接使用java语言来实现呢？），比如：
> native void Native4( int[] ary ) throws Exception ;
> private static native void setIn0(InputStream in);  //System类
> private static native void setOut0(PrintStream out);  //System类
> private static native void setErr0(PrintStream err);  //System类

native方法有效地扩展了jvm实现，比如java并发模块，很多方法是要直接和操作系统交互，比如线程优先级的映射等，多线程的状态，阻塞，锁等。native方法的实现让java和java之外的环境交互有了可能，如某些操作系统或某些底层硬件设备。

父类声明的 native 方法，子类可以重写，重写时可以依然声明为native方法，也可以移除native（这块看上去似乎有点奇怪）, 例如下面：

Foo.java
```java
public class Foo {
    public native void doSomething();
    public native int doPlus();
}
```

Son.java
```java
public class Son extends Foo{
    @Override
    public void doSomething() {
        System.out.println("do something");
    }

    @Override
    public native int doPlus();
}
```

app运行时，首先会将字节码文件加载到内存，字节码中方法描述符会指向native方法的直接实现，通常是DLL文件，这其实是通过java.system.loadLibrary()实现的，需要注意的是：native方法在提供可交互的同时会失去java的诸多好处，尽可能不要使用本地方法。


