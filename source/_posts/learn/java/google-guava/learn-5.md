---
title: Throwables类
date: 2017-10-22 16:05:53
categories:
- [学习, Java语言学习, GoogleGuava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
简化的异常处理

# 程序示例
```java
package com.example.google.guava.demo.clazz;
 
import com.example.google.guava.demo.exception.InvalidInputException;
import com.google.common.base.Throwables;
 
import java.io.IOException;
import java.util.List;
//import org.eclipse.jdt.core.compiler.InvalidInputException;
 
/**
 * <p>
 * <code>ThrowablesTest</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/19 9:57
 */
public class ThrowablesTest {
 
    public static void main(String[] args) {
 
        System.out.println("================1.InvalidInputException==================");
 
        try {
            showThrowableSqrt();
        }catch (InvalidInputException e){
            // Guava的异常链处理方法：获得根异常
            Throwable able = Throwables.getRootCause(e);
            // Guava的异常链处理方法：获得异常的列表
            List throwableList = Throwables.getCausalChain(e);
            System.out.println( able );
            System.out.println( throwableList );
        }
 
        System.out.println("================2.ArrayIndexOutOfBoundsException==================");
 
        try {
            showThrowableArray();
        }catch (Exception e){
            // Guava的异常链处理方法：获得异常堆栈的字符串
            String stackTrace = Throwables.getStackTraceAsString(e);
            System.out.println( stackTrace );
        }
 
        System.out.println("================3.IOException==================");
 
        try {
            showThrowableIO();
        }catch (Exception e){
            // 获取堆栈异常信息
            String stackTrace = Throwables.getStackTraceAsString(e);
            System.out.println( stackTrace );
        }
    }
 
    /**
     * 测试一
     */
    private static void showThrowableSqrt() throws InvalidInputException{
        try {
            sqrt(-3.0);
        } catch (Throwable e) {
            // 检测异常类型并抛出：
            /*
             * @deprecated Use {@link #throwIfInstanceOf}, which has the same behavior but rejects {@code
             *  null}. This method is scheduled to be removed in July 2018.
             */
            Throwables.propagateIfInstanceOf(e, InvalidInputException.class); // 此方法将于2018.7移除
            //Throwables.throwIfInstanceOf(e, InvalidInputException.class);
 
            /*
             * @deprecated Use {@code throw e} or {@code throw new RuntimeException(e)} directly, or use a
             *  combination of {@link #throwIfUnchecked} and {@code throw new RuntimeException(e)}. For
             *  background on the deprecation, read <a href="https://goo.gl/Ivn2kc">Why we deprecated
             *  {@code Throwables.propagate}</a>. This method is scheduled to be removed in July 2018.
            */
            Throwables.propagate(e);    // 此方法将于2018.7移除
            //Throwables.throwIfUnchecked(e);
 
        }
    }
 
    /**
     * 测试二
     */
    private static void showThrowableArray(){
        try {
            int[] data = {1,2,3};
            getValue(data, 4);
        } catch (Throwable e) {
            Throwables.propagateIfInstanceOf(e, IndexOutOfBoundsException.class);
            Throwables.propagate(e);
        }
    }
 
    /**
     * 测试三
     */
    private static void showThrowableIO(){
        try {
            dummyIO();
        } catch (Throwable e) {
            Throwables.propagateIfInstanceOf(e, IndexOutOfBoundsException.class);
            Throwables.propagate(e);
        }
    }
 
    /**
     * 自定义异常测试：InvalidInputException
     * 注意：此处的InvalidInputException不是org.eclipse.jdt.core.compiler.InvalidInputException
     * @param input 传入参数
     * @return 平方根
     * @throws InvalidInputException 自定义异常
     */
    private static double sqrt(double input) throws InvalidInputException{
        if(input < 0) throw new InvalidInputException("求平方根出了点问题，你再试试！");
        return Math.sqrt(input);
    }
 
    /**
     * 数组越界异常测试：IndexOutOfBoundsException
     * @param list 整型数组
     * @param index 数组长度
     * @return 数组指定元素
     * @throws IndexOutOfBoundsException 数组越界
     */
    private static double getValue(int[] list, int index) throws IndexOutOfBoundsException {
        return list[index];
    }
 
    /**
     * IO异常测试：IOException
     * @throws IOException io异常
     */
    private static void dummyIO() throws IOException {
        throw new IOException();
    }
}
```

# 输出结果
```
com.example.google.guava.demo.exception.InvalidInputException: 求平方根出了点问题，你再试试！
[com.example.google.guava.demo.exception.InvalidInputException: 求平方根出了点问题，你再试试！]
================2.ArrayIndexOutOfBoundsException==================
java.lang.ArrayIndexOutOfBoundsException: 4
	at com.example.google.guava.demo.clazz.ThrowablesTest.getValue(ThrowablesTest.java:132)
	at com.example.google.guava.demo.clazz.ThrowablesTest.showThrowableArray(ThrowablesTest.java:93)
	at com.example.google.guava.demo.clazz.ThrowablesTest.main(ThrowablesTest.java:42)
 
================3.IOException==================
java.lang.RuntimeException: java.io.IOException
	at com.google.common.base.Throwables.propagate(Throwables.java:241)
	at com.example.google.guava.demo.clazz.ThrowablesTest.showThrowableIO(ThrowablesTest.java:108)
	at com.example.google.guava.demo.clazz.ThrowablesTest.main(ThrowablesTest.java:52)
Caused by: java.io.IOException
	at com.example.google.guava.demo.clazz.ThrowablesTest.dummyIO(ThrowablesTest.java:140)
	at com.example.google.guava.demo.clazz.ThrowablesTest.showThrowableIO(ThrowablesTest.java:105)
	... 1 more
```

# 方法说明 - 传递异常的常用方法
```
1.RuntimeException propagate(Throwable)：把throwable包装成RuntimeException，用该方法保证异常传递，抛出一个RuntimeException异常
2.void propagateIfInstanceOf(Throwable, Class<X extends Exception>) throws X：当且仅当它是一个X的实例时，传递throwable
3.void propagateIfPossible(Throwable)：当且仅当它是一个RuntimeException和Error时，传递throwable
4.void propagateIfPossible(Throwable, Class<X extends Throwable>) throws X：当且仅当它是一个RuntimeException和Error时，或者是一个X的实例时，传递throwable。
```

# 附录 - InvalidInputException.java
```java
package com.example.google.guava.demo.exception;
 
 
/**
 * <p>
 * <code>InvalidInputException</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/19 10:19
 */
public class InvalidInputException extends Exception{
 
    public InvalidInputException( String message ){
        super( message );
    }
}
```

# 参考
http://www.yiibai.com/guava/guava_throwables_class.html

http://www.cnblogs.com/peida/p/Guava_Throwables.html

http://outofmemory.cn/java/guava/base/Throwables
