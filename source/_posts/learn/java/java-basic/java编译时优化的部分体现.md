---
title: java编译时优化的部分体现
date: 2017-12-07 09:19:53
tags:
- java
categories:
- [学习, Java语言学习, Java]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 如果使用“+”号拼接字符串，且“+”号两边都是常量，则jvm编译时做计算；如果存在变量，则在运行时做计算；

2. 使用final修饰的变量，可在jvm编译时做处理；

3. 使用String.intern()只要访问的常量相同，他们必然是同一个；

```java
/**
 * <p>
 * <code>Test</code>
 * </p>
 * Description: 《java特种兵》书中代码
 *
 * @author Mcchu
 * @date 2017/12/5 15:57
 */
public class Test {
 
    /**
     * JVM 的“编译时优化”：常量
     * 1.通过“+”赋值的，如果都是常量，由于JVM 的“编译时优化”，编译时会直接取最后值赋予变量
     * 2.通过“+”赋值的，如果包含变量，则在运行时再计算
     * 结论：String 的“+”操作并不一定比 StringBuilder.append()慢
     */
    private static void test1(){
        String a = "a"+"b"+1;     // a 引用是通过“+”赋值的，由于JVM 的“编译时优化”，会将其编译为：String a = "ab1"; 此三者都是常量无需运行时再进行计算
        String b = "ab1";         // b 引用是直接赋值的
        System.out.println(a==b); // 输出true
 
 
        String x = "a";           // x 为局部变量，由于运行时任何事情都会发生，如果存在代码切入，就可能发生改变
        String c = x+"b"+1;       // c 引用包含变量，由于局部变量的性质，故编译时无法赋值
        String d = "ab1";         // d 引用是直接赋值的
        System.out.println(c==d); // 输出false
    }
 
    /**
     * JVM 的“编译时优化”：final修饰符
     * 注：
     * 在编译阶段能确定的内容只能来自于常量池中，如 int、long、String 等常量，
     * 也就是不包含 new String()、new Integer()这样的操作，也不包含方法的返回值
     * 只有在编译阶段能确定这个 final 引用赋值的内容，编译器才有可能进行编译时优化
     */
    private static void test2(){
        String a = "a";
        final String b = "a";//final 修饰符，从定义上强制约束了 c 是不允许被改变的，由于 final 不可变，所以编译器自然认为结果是不可变的
 
        String c = a + "b";
        String d = b + "b";
        String e = getA() + "b";
 
        String compare = "ab";
        System.out.println( c==compare ); // 输出false
        System.out.println( d==compare ); // 输出true
        System.out.println( e==compare ); // 输出false
    }
    private static String getA(){
        return "a";
    }
 
    /**
     * JVM 的“编译时优化”：“常量池”区域对于同一个值的字符串保证全局唯一
     * 调用 intern()方法时，JVM 会在这个常量池中通过 equals()方法查找是否存在等值的 String
     *     如果存在，则直接返回常量池中这个 String 对象的地址；
     *     如不存在，则会创建等值的字符串（即等值的 char[]数组字符串，但是 char[]是新开辟的一份拷贝空间），然后再返回这个新创建空间的地址。
     * 只要是同样的字符串，当调用 intern()方法时，都会得到常量池中对应 String 的引用，所以两个字符串通过 intern()操作后用等号是可以匹配的。
     */
    private static void test3(){
        String a = "a";
        String b = a + "b";
        String c = "ab";
        String d = new String(b);
 
        System.out.println(b == c); // 输出false
        System.out.println(c == d); // 输出false
        System.out.println(c == d.intern()); // 输出true
        System.out.println(b.intern() == d.intern()); // 输出true
    }
 
    public static void main(String[] args) {
        System.out.println("测试一：");
        test1();
 
        System.out.println();
        System.out.println("测试二：");
        test2();
 
        System.out.println();
        System.out.println("测试三：");
        test3();
    }
}
```

输出结果：

```
测试一：
true
false
 
测试二：
false
true
false
 
测试三：
false
false
true
true
```
