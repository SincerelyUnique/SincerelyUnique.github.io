---
title: 简单了解java的StringBuilder扩容
date: 2019-08-27 12:28:59
tags:
- java
- StringBuilder
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
package com.example.demo.test;
 
 
/**
 * <p>
 * <code>StringBuilderTest</code>
 * </p>
 * Description: JDK 1.8
 *
 * @author Jalen
 * @date 2019/8/26 9:41
 */
public class StringBuilderTest {
 
    public static void main(String[] args) {
        //createString();
        //createEmptyStringBuilder();
        createNonEmptyStringBuilder();
    }
 
    private static void createString(){
        //编译期优化，直接将string赋值为"abc"，可以使用javap -verbose xxx.class查看
        String string = "a" + "b" + "c";
        System.out.println(string);
 
        //使用+号连接字符串等同于使用StringBuilder的append方法拼接字符串，
        //可以使用javap -verbose xxx.class查看字节码
        String a = "a";
        String b = "b";
        String c = "c";
        String str = a + b + c;
        System.out.println(str);
    }
 
    /**
     * 创建容量为空的StringBuilder
     * 调用链1：
     *      {@link java.lang.StringBuilder#StringBuilder}
     *          这里调用父类构造方法，同时赋值capacity为默认值16
     *      {@link java.lang.AbstractStringBuilder#AbstractStringBuilder(int)}
     *          这里初始化一个长度为16的字节数组(char[])
     * 调用链2：
     *      {@link java.lang.StringBuilder#append(String)}
     *          这里调用父类的append方法，将当前字符串传给父类
     *      {@link java.lang.AbstractStringBuilder#append(String)}
     *          if (str == null)
     *              return appendNull();
     *          int len = str.length();  //这里length为1
     *          ensureCapacityInternal(count + len); //第一次append，count为0
     *          str.getChars(0, len, value, count);
     *          count += len;  //count累加
     *          return this;
     *      {@link java.lang.AbstractStringBuilder#ensureCapacityInternal(int)}
     *          if (minimumCapacity - value.length > 0) { //这里minimumCapacity=1，value长度为16（即上面初始化时赋予的16个长度的容量）
     *             value = Arrays.copyOf(value, newCapacity(minimumCapacity));
     *          }
     *      {@link java.lang.String#getChars}
     *          if (srcBegin < 0) {
     *             throw new StringIndexOutOfBoundsException(srcBegin);
     *          }
     *          if (srcEnd > value.length) {
     *             throw new StringIndexOutOfBoundsException(srcEnd);
     *          }
     *          if (srcBegin > srcEnd) {
     *             throw new StringIndexOutOfBoundsException(srcEnd - srcBegin);
     *          }
     *          System.arraycopy(value, srcBegin, dst, dstBegin, srcEnd - srcBegin); //调用native方法将append的字符串以char数组形式copy到value里
     *       count += len; //count重新赋值
     * 调用链3：
     *      {@link java.lang.StringBuilder#append(int)}
     *      {@link java.lang.AbstractStringBuilder#append(int)}
     *          if (i == Integer.MIN_VALUE) {
     *             append("-2147483648");
     *             return this;
     *          }
     *          int appendedLength = (i < 0) ? Integer.stringSize(-i) + 1 : Integer.stringSize(i);
     *          int spaceNeeded = count + appendedLength;
     *          ensureCapacityInternal(spaceNeeded);
     *          Integer.getChars(i, spaceNeeded, value);
     *          count = spaceNeeded;
     *          return this;
     *      {@link java.lang.Integer#stringSize(int)}
     *          for (int i=0; ; i++)
     *             if (x <= sizeTable[i]) //sizeTable = { 9, 99, 999, 9999, 99999, 999999, 9999999, 99999999, 999999999, Integer.MAX_VALUE }
     *                 return i+1;
     *      {@link java.lang.AbstractStringBuilder#ensureCapacityInternal(int)}
     *          if (minimumCapacity - value.length > 0) {
     *             value = Arrays.copyOf(value, newCapacity(minimumCapacity));
     *          }
     *      {@link java.lang.Integer#getChars}
     *      count = count + appendedLength;
     * 调用链4：
     *      {@link java.lang.StringBuilder#append(boolean)}
     *      {@link java.lang.AbstractStringBuilder#append(boolean)}
     *      {@link java.lang.AbstractStringBuilder#ensureCapacityInternal(int)}
     * 调用链5：
     *      {@link java.lang.StringBuilder#append(char)}
     *      {@link java.lang.AbstractStringBuilder#append(char)}
     *      {@link java.lang.AbstractStringBuilder#ensureCapacityInternal(int)}
     * 调用链6：
     *      {@link java.lang.StringBuilder#append(long)}
     *      {@link java.lang.AbstractStringBuilder#append(long)}
     *         if (l == Long.MIN_VALUE) {
     *             append("-9223372036854775808");
     *             return this;
     *         }
     *         int appendedLength = (l < 0) ? Long.stringSize(-l) + 1
     *                                      : Long.stringSize(l);
     *         int spaceNeeded = count + appendedLength;
     *         ensureCapacityInternal(spaceNeeded);
     *         Long.getChars(l, spaceNeeded, value);
     *         count = spaceNeeded;
     *         return this;
     */
    @SuppressWarnings("all")
    private static void createEmptyStringBuilder(){
        //这里等同于上面的String str = a + b + c
        StringBuilder sb = new StringBuilder(); //1
        sb.append("a");                         //2
        sb.append("b");
        sb.append(1);                           //3
        sb.append(true);                        //4
        sb.append('c');                         //5
        sb.append(99L);                         //6
        System.out.println(sb.toString());
    }
 
    /**
     * 创建包含初始容量的StringBuilder
     * 调用链1：
     *      {@link java.lang.StringBuilder#StringBuilder(int)}
     *      {@link java.lang.AbstractStringBuilder#AbstractStringBuilder(int)}
     *          value = new char[capacity]; //capacity=3
     * 调用链2：
     *      {@link java.lang.StringBuilder#append(String)}
     *      {@link java.lang.AbstractStringBuilder#append(String)}
     *      {@link java.lang.AbstractStringBuilder#ensureCapacityInternal(int)}
     *         if (minimumCapacity - value.length > 0) { //minimumCapacity=2, value.length=3
     *             value = Arrays.copyOf(value, newCapacity(minimumCapacity));
     *         }
     *      {@link java.lang.String#getChars}
     *          System.arraycopy(value, srcBegin, dst, dstBegin, srcEnd - srcBegin);
     *      count += len;
     * 调用链3：
     *      {@link java.lang.StringBuilder#append(String)}
     *      {@link java.lang.AbstractStringBuilder#append(String)}
     *      {@link java.lang.AbstractStringBuilder#ensureCapacityInternal(int)}
     *         if (minimumCapacity - value.length > 0) { //minimumCapacity=4, value.length=3
     *             value = Arrays.copyOf(value, newCapacity(minimumCapacity)); //value扩容
     *         }
     *      {@link java.util.Arrays#copyOf(float[], int)}
     *         float[] copy = new float[newLength];
     *         System.arraycopy(original, 0, copy, 0, Math.min(original.length, newLength));
     *         return copy;
     *      {@link java.lang.AbstractStringBuilder#newCapacity(int)}
     *         int newCapacity = (value.length << 1) + 2; //value.length=3, newCapacity=8
     *         if (newCapacity - minCapacity < 0) { //minCapacity=4
     *             newCapacity = minCapacity;
     *         }
     *         return (newCapacity <= 0 || MAX_ARRAY_SIZE - newCapacity < 0)
     *             ? hugeCapacity(minCapacity) : newCapacity;
     *      {@link java.lang.String#getChars}
     *          System.arraycopy(value, srcBegin, dst, dstBegin, srcEnd - srcBegin);
     *      count += len;
     */
    @SuppressWarnings("all")
    private static void createNonEmptyStringBuilder(){
        StringBuilder sb = new StringBuilder(3); //1
        sb.append("aa"); //2
        sb.append("bb"); //3，触发扩容
        System.out.println(sb.toString());
    }
}
```

扩容方法

```
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
    public static final int MAX_VALUE = 0x7fffffff;
 
    private int newCapacity(int minCapacity) {
        // overflow-conscious code
        int newCapacity = (value.length << 1) + 2;
        if (newCapacity - minCapacity < 0) {
            newCapacity = minCapacity;
        }
        return (newCapacity <= 0 || MAX_ARRAY_SIZE - newCapacity < 0)
            ? hugeCapacity(minCapacity)
            : newCapacity;
    }
 
 
    private int hugeCapacity(int minCapacity) {
        if (Integer.MAX_VALUE - minCapacity < 0) { // overflow
            throw new OutOfMemoryError();
        }
        return (minCapacity > MAX_ARRAY_SIZE)
            ? minCapacity : MAX_ARRAY_SIZE;
    }
```

StringBuffer扩容方式与StringBuilder基本一致
