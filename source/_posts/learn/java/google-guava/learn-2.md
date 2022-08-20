---
title: Preconditions类
date: 2017-10-19 16:05:50
categories:
- [学习, Java语言学习, GoogleGuava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
自定义异常信息

# 程序示例
```java
package com.example.google.guava.demo.clazz;
 
import com.google.common.base.Preconditions;
 
/**
 * <p>
 * <code>PreconditionsTest</code>
 * </p>
 * Description:Preconditions提供静态方法来检查方法或构造函数，被调用是否给定适当的参数。它检查的先决条件。其方法失败抛出IllegalArgumentException。
 *
 * @author Mcchu
 * @date 2017/10/18 16:21
 */
public class PreconditionsTest {
 
    public static void main(String[] args) {
 
        try {
            System.out.println(sqrt(-3.0));
        }catch(IllegalArgumentException e){
            System.out.println(e.getMessage());
        }
 
        try {
            System.out.println(sum(null,3));
        }catch(NullPointerException e){
            System.out.println(e.getMessage());
        }
 
        try {
            System.out.println(getValue(6));
        }catch(IndexOutOfBoundsException e){
            System.out.println(e.getMessage());
        }
 
        try {
            System.out.println(overOne(0));
        }catch (IllegalStateException e){
            System.out.println(e.getMessage());
        }
    }
 
    // 平方根
    private static double sqrt(double input) throws IllegalArgumentException {
        Preconditions.checkArgument(input > 0.0,
                "异常信息：此参数不大于0，无法开平方根 %s.", input);
        return Math.sqrt(input);
    }
 
    // 求和
    private static int sum(Integer a, Integer b){
        a = Preconditions.checkNotNull(a,
                "异常信息：第一个参数为null.");
        b = Preconditions.checkNotNull(b,
                "异常信息：第二个参数为null.");
        return a+b;
    }
 
    // 判断数组长度
    private static int getValue(int input){
        int[] data = {1,2,3,4,5};
        Preconditions.checkElementIndex(input,data.length,
                "异常信息：数组长度越界.");
        return 0;
    }
 
    // 判断状态
    private static int overOne(int input){
        Preconditions.checkState( input>1,
                "异常信息：传入数据不大于1" );
        return 0;
    }
}
```

# 输出结果
```
异常信息：此参数不大于0，无法开平方根 -3.0.
异常信息：第一个参数为null.
异常信息：数组长度越界. (6) must be less than size (5)
异常信息：传入数据不大于1
```

参考：http://www.yiibai.com/guava/guava_preconditions_class.html
