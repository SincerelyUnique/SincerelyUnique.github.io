---
title: java进制转换
date: 2017-12-07 10:52:48
tags:
- java
categories:
- [学习, Java语言学习, Java]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

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
 
    public static void main(String[] args) {
 
        //二进制转换
        System.out.println("二进制转换：");
        String s1 = Integer.toBinaryString(10);
        String s2 = Long.toBinaryString(10L);
        System.out.println(s1);
        System.out.println(s2);
 
        //十六进制转换
        System.out.println("十六进制转换：");
        String s3 = Integer.toHexString(10);
        String s4 = Long.toHexString(10L);
        String s5 = Float.toHexString(10F);
        String s6 = Double.toHexString(10D);
        System.out.println(s3);
        System.out.println(s4);
        System.out.println(s5);
        System.out.println(s6);
 
        //十进制转换
        System.out.println("十进制转换：");
        int s7 = Integer.parseInt("10");
        System.out.println(s7);
 
        int s8 = Integer.parseInt("10",16);
        int s9 = Integer.parseInt("10",10);
        int s10 = Integer.parseInt("10",8);
        int s11 = Integer.parseInt("10",2);
        System.out.println(s8);
        System.out.println(s9);
        System.out.println(s10);
        System.out.println(s11);
 
 
        int s12 = Integer.valueOf("10",16);
        int s13 = Integer.valueOf("10",10);
        int s14 = Integer.valueOf("10",8);
        int s15 = Integer.valueOf("10",2);
        System.out.println(s12);
        System.out.println(s13);
        System.out.println(s14);
        System.out.println(s15);
    }
}
```

输出：

```
二进制转换：
1010
1010
十六进制转换：
a
a
0x1.4p3
0x1.4p3
十进制转换：
10
16
10
8
2
16
10
8
2
```
