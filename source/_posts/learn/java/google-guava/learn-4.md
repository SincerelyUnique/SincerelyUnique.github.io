---
title: Range类
date: 2017-10-21 16:05:52
categories:
- [学习, Java语言学习, Google Guava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
数据范围处理

# 程序示例
```java
package com.example.google.guava.demo.clazz;
 
import com.google.common.collect.ContiguousSet;
import com.google.common.collect.DiscreteDomain;
import com.google.common.collect.Range;
import com.google.common.primitives.Ints;
 
/**
 * <p>
 * <code>RangeTest</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/19 9:06
 */
public class RangeTest {
 
    public static void main(String[] args) {
        testRange();
    }
 
    private static void testRange(){
        // 1.使用Range.closed()创建范围: [a,b] = { x | a <= x <= b}
        Range<Integer> range1 = Range.closed(0, 9);
        System.out.println("新建数据范围range1："+range1);
        printRange(range1);
 
        // 1.1包含关系
        Boolean containVal = range1.contains(1);
        Boolean containAllVal1 = range1.containsAll(Ints.asList(1,3,5));
        Boolean containAllVal2 = range1.containsAll(Ints.asList(1,3,5,12));
        System.out.println("是否包含1："+containVal);
        System.out.println("是否包含1，3，5："+containAllVal1);
        System.out.println("是否包含1，3，5，12："+containAllVal2);
 
        // 1.2边界值
        Boolean bol1 = range1.hasLowerBound();
        Boolean bol2 = range1.hasUpperBound();
        System.out.println("是否存在最小边界值："+bol1);
        System.out.println("是否存在最大边界值："+bol2);
 
        Integer lower = range1.lowerEndpoint();
        Integer upper = range1.upperEndpoint();
        System.out.println("最小边界值："+lower);
        System.out.println("最大边界值："+upper);
        System.out.println();
 
 
        // 2.使用Range.open()创建范围: (a,b) = { x | a < x < b}
        Range<Integer> range2 = Range.open(0,9);
        System.out.println("新建数据范围range2："+range2);
        printRange(range2);
        System.out.println();
 
 
        // 3.使用Range.openClosed()创建范围： (a,b] = { x | a < x <= b}
        Range<Integer> range3 = Range.openClosed(0, 9);
        System.out.println("新建数据范围range3："+range3);
        printRange(range3);
        System.out.println();
 
 
        // 4.使用Range.closedOpen()创建范围： [a,b) = { x | a <= x < b}
        Range<Integer> range4 = Range.closedOpen(0, 9);
        System.out.println("新建数据范围range4："+range4);
        printRange(range4);
        System.out.println();
 
 
        // 5.右无穷大 a>9
        Range<Integer> range5 = Range.greaterThan(9);
        System.out.println("新建数据范围range5："+range5);
 
        // 5.1边界值
        Boolean bol3 = range5.hasLowerBound();
        Boolean bol4 = range5.hasUpperBound();
        System.out.println("是否存在最小边界值："+bol3);
        System.out.println("是否存在最大边界值："+bol4);
 
        Integer lower1 = range5.lowerEndpoint();
        //Integer upper1 = range5.upperEndpoint(); //抛java.lang.IllegalStateException: range unbounded on this side
        System.out.println("最小边界值："+lower1);
        //System.out.println("最大边界值："+upper1);
        System.out.println();
 
        // 6.子范围
        Range<Integer> range6 = Range.closed(3, 5);
        System.out.println("新建数据范围："+range6);
        printRange(range6);
        Boolean subRange = range1.encloses(range6);
        System.out.println("范围range1是否包含range6："+subRange);
        System.out.println();
 
 
        // 7.承接关系
        Range<Integer> range7 = Range.closed(9, 20);
        System.out.println("新建数据范围："+range7);
        printRange(range7);
        Boolean connected = range7.isConnected(range1);
        System.out.println("范围range7是否承接range1："+connected);
        System.out.println();
 
 
        // 8.范围交叉,取交集、并集
        Range<Integer> range8 = Range.closed(5, 15);
        Range<Integer> intersection = range1.intersection(range8);
        System.out.println("范围range1和range8的交集："+intersection);
        printRange(intersection);
        Range<Integer> span = range1.span(range8);
        System.out.println("范围range1和range8的并集："+span);
        printRange(span);
 
    }
 
    private static void printRange(Range<Integer> range){
        System.out.print("具体数据范围：");
        System.out.print("[ ");
        for(int grade : ContiguousSet.create(range, DiscreteDomain.integers())) {
            System.out.print(grade +" ");
        }
        System.out.println("]");
    }
}
```

# 输出结果
```
新建数据范围range1：[0..9]
具体数据范围：[ 0 1 2 3 4 5 6 7 8 9 ]
是否包含1：true
是否包含1，3，5：true
是否包含1，3，5，12：false
是否存在最小边界值：true
是否存在最大边界值：true
最小边界值：0
最大边界值：9
 
新建数据范围range2：(0..9)
具体数据范围：[ 1 2 3 4 5 6 7 8 ]
 
新建数据范围range3：(0..9]
具体数据范围：[ 1 2 3 4 5 6 7 8 9 ]
 
新建数据范围range4：[0..9)
具体数据范围：[ 0 1 2 3 4 5 6 7 8 ]
 
新建数据范围range5：(9..+∞)
是否存在最小边界值：true
是否存在最大边界值：false
最小边界值：9
 
新建数据范围：[3..5]
具体数据范围：[ 3 4 5 ]
范围range1是否包含range6：true
 
新建数据范围：[9..20]
具体数据范围：[ 9 10 11 12 13 14 15 16 17 18 19 20 ]
范围range7是否承接range1：true
 
范围range1和range8的交集：[5..9]
具体数据范围：[ 5 6 7 8 9 ]
范围range1和range8的并集：[0..15]
具体数据范围：[ 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 ]
```

参考：http://www.yiibai.com/guava/guava_range_class.html
