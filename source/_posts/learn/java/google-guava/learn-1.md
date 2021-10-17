---
title: (1) Optional类
date: 2017-10-18 16:05:49
categories:
- [学习, Java语言学习, Google Guava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
null处理，避免NPE

# 程序示例
```java
package com.example.google.guava.demo.clazz;
 
 
import com.google.common.base.Optional;
 
/**
 * <p>
 * <code>OptionalTest</code>
 * </p>
 * Description:输出结果
 * > First parameter is present: false
 * > Second parameter is present: true
 * > 10
 *
 * @author Mcchu
 * @date 2017/10/18 15:02
 */
public class OptionalTest {
 
    public static void main(String[] args) {
        OptionalTest test = new OptionalTest();
 
        Integer value1 = null;
        Integer value2 = 10;
 
        // 允许传null
        Optional<Integer> a1 = Optional.fromNullable(value1);   //输出：Optional.absent()
        //Optional<Integer> a2 = Optional.fromNullable(value2);   //输出：Optional.of(10)
 
        // java.util.Optional<T> 是java8新增
        //java.util.Optional<Integer> b1 = java.util.Optional.ofNullable(value1);  //输出：Optional.empty
        //java.util.Optional<Integer> b2 = java.util.Optional.ofNullable(value2);  //输出：Optional[10]
 
        // 传null抛NPE
        //Optional<Integer> c1 = Optional.of(value1);     //输出：java.lang.NullPointerException
        Optional<Integer> c2 = Optional.of(value2);     //输出：Optional.of(10)
 
        Integer result = sum(a1,c2);
        System.out.println(result);     //输出：10
    }
 
    private static Integer sum(Optional<Integer> a,Optional<Integer> b){
        // 判断a、b是否出现（英文翻译：present：出现；absent：未出现，缺席）
        System.out.println("First parameter is present: " + a.isPresent());     //输出：false
        System.out.println("Second parameter is present: " + b.isPresent());    //输出：true
 
        // 如果值存在，则返回初始值，否则返回默认值，or()方法中是默认值
        Integer d1 = a.or(0);   //输出：0
        //Integer d2 = b.or(0);   //输出：10
 
        //Integer e1 = a.get();   //输出：java.lang.IllegalStateException: Optional.get() cannot be called on an absent value
        Integer e2 = b.get();   //输出：10
 
        return d1 + e2;
    }
}
```

# 输出结果
```
First parameter is present: false
Second parameter is present: true
10
```

参考：http://www.yiibai.com/guava/guava_optional_class.html
