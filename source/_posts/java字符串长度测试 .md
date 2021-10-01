---
title: java字符串长度测试
date: 2019-08-23 13:09:38
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
package com.example.demo.test;
 
import java.lang.management.ManagementFactory;
import java.lang.management.MemoryMXBean;
import java.lang.management.MemoryUsage;
import java.util.List;
 
/**
 * <p>
 * <code>Test</code>
 * </p>
 * Description: String
 * 1)The value is used for character storage. String本质上是以char[]形式存储的
 * 2)Note that is unnecessary since Strings are immutable. 如果字符串不可改变（常量），有些构造方法是不需要用到的
 *
 * @author Jalen
 * @date 2019/8/19 11:20
 */
public class Test {
 
    public static void main(String[] args) {
        //defineString();
        //getVMOptions();
        testMaxLength();
    }
 
    private static void defineString(){
        String str11 = new String(); //等价于String str12 = "";
        String str12 = "";
        String str13 = null;
        System.out.println(str11);
        System.out.println(str12);
        System.out.println(str13);
 
        String temp = "old value";
        String str2 = new String(temp); //等价于String str2 = temp
        temp = "new value";
        System.out.println(temp);
        System.out.println(str2);
 
        char[] charArray = {'b', 'i', 'r', 't', 'h', ' ', 'd', 'a', 'y'};
        String str3 = new String(charArray);
        charArray[0] = 'm';
        System.out.println(charArray);
        System.out.println(str3);
 
        String str4 = new String(charArray, 6, 3);
        charArray[6] = 'p';
        System.out.println(charArray);
        System.out.println(str4);
 
        int[] intArray = {1, 2, 3, 4, 5};
        String str5 = new String(intArray, 2, 3);
        System.out.println(str5);
    }
 
    private static void testMaxLength(){
        //当长度超过65534时编译报错：Error: constant string too long
        String newStr = "aaaa ... ... aaaa"; //65535个a
        System.out.println("newStr length: " + newStr.length());
        System.out.println(newStr);
 
        char[] charArray = new char[65535 * 3];
        for (int i = 0; i < charArray.length; i++) {
            charArray[i] = 'a';
        }
        String str1 = new String(charArray);
        System.out.println("str1 length: " + str1.length());
        System.out.println(str1);
 
        String str2 = new String(charArray, 65535, 65535 * 2 - 1);
        System.out.println("str2 length: " + str2.length());
        System.out.println(str2);
 
        int max = Integer.MAX_VALUE;
        System.out.println("int max value: " + max);
        long availableHeapSize = getVMOptions();
        System.out.println("available heap size: " + availableHeapSize);
        //char[] newCharArray = new char[max - 1]; // OOM(Requested array size exceeds VM limit),java限制数组长度不能超过max-2
        //char[] newCharArray = new char[max - 2]; // OOM(Java heap space)
        //long maxLength = availableHeapSize/6 * 5; // java.lang.NegativeArraySizeException, 负数
        long maxLength = availableHeapSize/14 * 6;
        System.out.println(maxLength);
        char[] newCharArray = new char[(int) maxLength];
        for (int i = 0; i < newCharArray.length ; i++) {
            newCharArray[i] = 'a';
        }
        String str3 = new String(newCharArray);
        System.out.println("str3 length: " + str3.length());
        System.out.println(str3);
    }
 
    private static long getVMOptions(){
        MemoryMXBean memoryMXBean = ManagementFactory.getMemoryMXBean();
        MemoryUsage usage = memoryMXBean.getHeapMemoryUsage();
        System.out.println("INT HEAP:" + usage.getInit()/1024/1024 + "Mb");
        System.out.println("MAX HEAP:" + usage.getMax()/1024/1024 + "Mb");
        System.out.println("USED HEAP:" + usage.getUsed()/1024/1024 + "Mb");
 
        System.out.println("\nINT HEAP:" + usage.getInit() + "bytes");
        System.out.println("MAX HEAP:" + usage.getMax() + "bytes");
        System.out.println("USED HEAP:" + usage.getUsed() + "bytes");
 
        System.out.println("\nFull Information:");
        System.out.println("Heap Memory Usage:" + memoryMXBean.getHeapMemoryUsage());
        System.out.println("Non-Heap Memory Usage:" + memoryMXBean.getNonHeapMemoryUsage());
 
        List<String> inputArguments = ManagementFactory.getRuntimeMXBean().getInputArguments();
        System.out.println("=====================java options==================");
        System.out.println(inputArguments);
 
        System.out.println("=====================通过java来获取相关系统状态====================");
        long i = Runtime.getRuntime().totalMemory()/1024/1024;//Java 虚拟机中的内存总量，以字节为单位
        System.out.println("总的内存量为:" + i + "Mb");
        long j = Runtime.getRuntime().freeMemory()/1024/1024;//Java 虚拟机中的空闲内存量
        System.out.println("空闲内存量:" + j + "Mb");
        long k = Runtime.getRuntime().maxMemory()/1024/1024;
        System.out.println("最大可用内存量:" + k + "Mb");
        return (usage.getMax() - usage.getUsed());
    }
}
```
