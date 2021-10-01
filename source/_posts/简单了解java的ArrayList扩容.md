---
title: 简单了解java的ArrayList扩容
date: 2019-08-26 12:33:31
tags:
- java
- ArrayList
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
package com.example.demo.test;
 
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
 
/**
 * <p>
 * <code>ArrayListTest</code>
 * </p>
 * Description: JDK 1.8 普通集合类
 * {@link java.util.ArrayList} 底层其实就是一个Object[]
 *
 * @author Jalen
 * @date 2019/8/26 9:40
 */
public class ArrayListTest {
 
    /**
     * Test method
     * @param args arguments
     */
    public static void main(String[] args) {
        createArrayList();
    }
 
    /**
     * @see java.util.ArrayList#ArrayList() 创建一个空的list,底层其实是一个空的对象数组（final Object[] elementData = {}）
     * @see java.util.ArrayList#add 添加元素。计算容量，设置容量，赋值
     *  {@link java.util.ArrayList#ensureCapacityInternal(int)}  minCapacity=size + 1， size default is 0
     *      {@link java.util.ArrayList#calculateCapacity(Object[], int)}  elementData legth is 0, minCapacity is 1
     *      {@link java.lang.Math#max(int, int)} if elementData legth is 0，then default is 10，compare with minCapacity, choose the bigger one as capacity
     *  {@link java.util.ArrayList#ensureExplicitCapacity(int)} modCount++(iterator operation), if elementData length(0) lower than minCapacity(10)，then grow
     *      {@link java.util.ArrayList#grow(int)} oldCapacity is 0, newCapacity = minCapacity is 10
     *      {@link java.util.Arrays#copyOf(Object[], int)} set 10 length array obj copy as new elementData
     *      elementData[0] = "";  add value what you want
     * @see java.util.ArrayList#ArrayList(int) elementData = new Object[initialCapacity]
     */
    private static void createArrayList(){
        ArrayList<String> list = new ArrayList<>();
        list.add(generateRandomString());
        System.out.println(list.toString());
 
        ArrayList<String> newList = new ArrayList<>(5);
        newList.add(generateRandomString());  //默认5个长度的空数组（elementData length是5），当前需添加一个长度（minCapacity是1），直接赋值
        System.out.println(newList.toString());
 
        ArrayList<String> newerList = new ArrayList<>(5);
        System.out.println("size: " + newerList.size());
        newerList.add(generateRandomString());
        System.out.println("size: " + newerList.size());
        newerList.add(generateRandomString());
        newerList.add(generateRandomString());
        newerList.add(generateRandomString());
        newerList.add(generateRandomString());
        newerList.add(generateRandomString());  // 当添加第6个元素时，计算capacity不足，触发grow扩容，扩容到7
        newerList.add(generateRandomString());
        newerList.add(generateRandomString());  // 当添加第8个元素时，计算capacity不足，触发grow扩容，扩容到10
        newerList.add(generateRandomString());
        newerList.add(generateRandomString());
        newerList.add(generateRandomString());  // 当添加第11个元素时，计算capacity不足，触发grow扩容，扩容到15
        newerList.add(generateRandomString());
        newerList.add(generateRandomString());
        newerList.add(generateRandomString());
        System.out.println("size: " + newerList.size());  // 扩容公式为：oldCapacity + oldCapacity >> 1 = newCapacity
        System.out.println(newerList.toString());
 
        ArrayList<String> arrayList = new ArrayList<>(20);
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        arrayList.add(generateRandomString());
        System.out.println(arrayList.toString());  // 减少扩容次数
 
        //插入操作，会引发扩容，且包含数组越界检测
        ArrayList<String> firstList = new ArrayList<>(5);
        firstList.add(generateRandomString());
        firstList.add(generateRandomString());
        firstList.add(generateRandomString());
        firstList.add(generateRandomString());
        firstList.add(2, "China");  //rangeCheckForAdd->ensureCapacityInternal->System.arraycopy
        firstList.add(4, "America"); //rangeCheckForAdd->ensureCapacityInternal(grow)->System.arraycopy
        System.out.println(firstList.toString()); //超出容量，必先扩容，然后讲当前list第二位向后移动，空出第二位，再给第二位赋值
 
        //修改操作，不存在扩容，但包含数据越界检测
        ArrayList<String> secondList = new ArrayList<>(5);
        secondList.add(generateRandomString());
        secondList.add(generateRandomString());
        secondList.add(generateRandomString());
        secondList.add(generateRandomString());
        secondList.set(2, "China"); //rangeCheck->get oldValue(index为2的旧值)->set newValue to "China"->return oldValue
        //secondList.set(4, "America"); //index=4, listSize=4, index>=listSize, throw exception
        System.out.println(secondList.toString());
 
        //根据下标截取subList
        ArrayList<String> thirdList = new ArrayList<>(5);
        thirdList.add("China");
        thirdList.add("America");
        thirdList.add("Russia");
        thirdList.add("England");
        thirdList.add("France");
        List<String> subThirdList = thirdList.subList(3, 5); //subListRangeCheck->SubList
        System.out.println(subThirdList.toString());
    }
 
    /**
     * 生成6个字节长度的随机字符串
     * @return 字符串
     */
    private static String generateRandomString() {
        char[] ch = new char[6];
        for (int i = 0; i < 6; i++) {
            Random rnd = new Random();
            int d = rnd.nextInt(26) + 97;
            char c = (char) d;
            ch[i] = c;
        }
        return new String(ch);
    }
}
```

扩容公式：

```
    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
