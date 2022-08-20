---
title: Multiset接口
date: 2017-10-23 16:05:54
categories:
- [学习, Java语言学习, GoogleGuava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
简化set集合操作

# 程序示例
```java
package com.example.google.guava.demo.collection;
 
import com.google.common.collect.HashMultiset;
import com.google.common.collect.Multiset;
 
import java.util.Arrays;
import java.util.Iterator;
import java.util.Set;
 
/**
 * <p>
 * <code>MultisetTest</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/19 12:00
 */
public class MultisetTest {
 
    public static void main(String[] args) {
        Multiset<String> multiset = HashMultiset.create();
        multiset.add("a");
        multiset.add("b");
        multiset.add("c");
        multiset.add("d");
        multiset.add("a");
        multiset.add("b");
        multiset.add("c");
        multiset.add("b");
        multiset.add("b");
        multiset.add("b");
 
        // 1.元素数量
        Integer size = multiset.size();
        System.out.println( "1.集合长度大小："+size );    //输出：10
 
        // 2.“b”元素数量
        Integer countElementB = multiset.count("b");
        System.out.println( "2.元素b的出现次数："+countElementB ); //输出：5
 
        // 3.去重
        Set<String> set = multiset.elementSet();
        System.out.print("3.去除重复元素：");
        for ( String element: set ){
            System.out.print(element);
            System.out.print(",");
        }
 
        // 4.迭代
        Iterator<String> it  = multiset.iterator();
        System.out.println();
        System.out.print("4.使用iterator迭代：");
        while(it.hasNext()){
            System.out.print(it.next());
        }
 
        // 5.元素出现次数统计
        System.out.println();
        System.out.println("5.元素出现次数统计：");
        for (Multiset.Entry<String> entry : multiset.entrySet()){
            String element = entry.getElement();
            Integer count = entry.getCount();
            System.out.println("  元素："+ element +", 出现次数：" + count);
        }
 
        // 6.移除元素:移除两个“b”
        multiset.remove("b",2);
        Integer countRestB = multiset.count("b");
        System.out.println("6.剩下的b元素个数："+countRestB);
 
        // 7.包含判断
        Boolean checkExist = multiset.contains("b");
        Boolean checkExistAll = multiset.containsAll(Arrays.asList("a","b"));
        System.out.println("7.包含关系：");
        System.out.println("  是否包含b元素："+checkExist);  //输出：true
        System.out.println("  是否包含a、b元素："+checkExistAll);  //输出：true
 
        // 8.字符串打印
        String str = multiset.toString();
        System.out.println("8.转为字符串："+str);
    }
}
```

# 输出结果
```
1.集合长度大小：10
2.元素b的出现次数：5
3.去除重复元素：a,b,c,d,
4.使用iterator迭代：aabbbbbccd
5.元素出现次数统计：
  元素：a, 出现次数：2
  元素：b, 出现次数：5
  元素：c, 出现次数：2
  元素：d, 出现次数：1
6.剩下的b元素个数：3
7.包含关系：
  是否包含b元素：true
  是否包含a、b元素：true
8.转为字符串：[a x 2, b x 3, c x 2, d]
```

参考：http://www.yiibai.com/guava/guava_multiset.html
