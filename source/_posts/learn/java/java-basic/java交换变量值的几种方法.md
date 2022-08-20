---
title: java交换变量值的几种方法
date: 2017-12-07 10:02:29
tags:
- java
categories:
- [学习, Java语言学习, Java]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 使用中间量存放某一变量值，再分别赋值；

2. 使用加法求和，再做减法；

3. 使用异或；

如2，如果做加法，可能会越界，但是输出结果是正确的。

由2可知，做乘法、除法等运算在一定条件下也是适用的。

由3可知，除了异或，存在一些其他位运算在一定条件下也是适用的。

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
     * 使用中间量
     * @param a a
     * @param b b
     */
    private static void test1( String a ,String b ){
        String c = a;
        a = b;
        b = c;
        System.out.println(a);
        System.out.println(b);
    }
 
    /**
     * 使用中间量，同上
     * @param a a
     * @param b b
     */
    private static void test2( String a ,String b ){
        String c = b;
        b = a;
        a = c;
        System.out.println(a);
        System.out.println(b);
    }
 
    /**
     * 先求和，再求反向求差，小心越界
     * @param a a
     * @param b b
     */
    private static void test3( int a,int b ){
        int sum = a + b;
        a = sum - a;
        b = sum - b;
        System.out.println(a);
        System.out.println(b);
    }
 
    /**
     * 位运算，异或,反推
     * @param a a
     * @param b b
     */
    private static void test4( int a,int b ){
        a = a ^ b;
        b = a ^ b;
        a = a ^ b;
        System.out.println(a);
        System.out.println(b);
    }
 
    public static void main(String[] args) {
        System.out.println("使用中间量：");
        test1("hello","world");
        test2("hello","world");
 
        System.out.println();
        System.out.println("先求和再做减法：");
        test3(3,5);
        test3(2147483647,10); //2147483647是int最大值
        test3(2147483647,3700000*120*430*230);
 
        System.out.println();
        System.out.println("异或：");
        test4(3,5);
        test4(2147483647,3700000*120*430*230);
    }
}
```

输出：

```
使用中间量：
world
hello
world
hello
 
先求和再做减法：
5
3
10
2147483647
-145634304
2147483647
 
异或：
5
3
-145634304
2147483647
```
