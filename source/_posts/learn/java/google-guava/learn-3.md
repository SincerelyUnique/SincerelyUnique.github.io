---
title: (3) Ordering类
date: 2017-10-20 16:05:51
categories:
- [学习, Java语言学习, Google Guava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
排序

# 程序示例
```java
package com.example.google.guava.demo.clazz;
 
import com.google.common.collect.Ordering;
 
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
 
/**
 * <p>
 * <code>OrderingTest</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/18 16:54
 */
public class OrderingTest {
 
    public static void main(String[] args) {
        List<Integer> numList = new ArrayList<>();
        numList.add(5);
        numList.add(30);
        numList.add(25);
        numList.add(10);
        numList.add(15);
        System.out.println("排序前："+numList);
 
        // 1.自然排序，由小到大
        Ordering ordering = Ordering.natural();
        Collections.sort(numList,ordering);
        System.out.println("自然排序："+numList);
 
        // 2.判断是否有序，最小值，最大值
        Boolean bol = ordering.isOrdered(numList);
        Integer minValue = (Integer) ordering.min(numList);
        Integer maxValue = (Integer) ordering.max(numList);
        System.out.println("是否已排序："+bol);
        System.out.println("最小值："+minValue);
        System.out.println("最大值："+maxValue);
 
        // 3.反序
        Collections.sort(numList,ordering.reverse());
        System.out.println("反序排列："+numList);
 
        // 4.添加一个null值
        numList.add(null);
        System.out.println("添加null值后："+numList);
 
        // 5.null存放在前、后
        Collections.sort(numList,ordering.nullsFirst());
        System.out.println("null在前："+numList);
        Collections.sort(numList,ordering.nullsLast());
        System.out.println("null在后："+numList);
 
        System.out.println("===========================");
 
        // 6.字符串null
        List<String> names = new ArrayList<String>();
        names.add("Ram");
        names.add("Shyam");
        names.add("Mohan");
        names.add("Sohan");
        names.add("Ramesh");
        names.add("Suresh");
        names.add("Naresh");
        names.add("Mahesh");
        names.add(null);
        names.add("Vikas");
        names.add("Deepak");
        System.out.println("排序前："+names);
 
        Collections.sort(names,ordering.nullsFirst().reverse());
        System.out.println("排序后："+names);
    }
}
```

# 输出结果
```
排序前：[5, 30, 25, 10, 15]
自然排序：[5, 10, 15, 25, 30]
是否已排序：true
最小值：5
最大值：30
反序排列：[30, 25, 15, 10, 5]
添加null值后：[30, 25, 15, 10, 5, null]
null在前：[null, 5, 10, 15, 25, 30]
null在后：[5, 10, 15, 25, 30, null]
===========================
排序前：[Ram, Shyam, Mohan, Sohan, Ramesh, Suresh, Naresh, Mahesh, null, Vikas, Deepak]
排序后：[Vikas, Suresh, Sohan, Shyam, Ramesh, Ram, Naresh, Mohan, Mahesh, Deepak, null]
```

参考：http://www.yiibai.com/guava/guava_ordering_class.html
