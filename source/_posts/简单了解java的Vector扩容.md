---
title: 简单了解java的Vector扩容
date: 2019-08-27 14:19:34
tags:
- java
- Vector
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
package com.example.demo.test;
 
import java.util.Vector;
 
/**
 * <p>
 * <code>VectorTest</code>
 * </p>
 * Description: JDK 1.8
 * 测试Vector的扩容方式，通过测试可以看出其每次以2的倍数形式扩充Object数组
 *
 * @author Jalen
 * @date 2019/8/27 13:31
 */
public class VectorTest {
 
    public static void main(String[] args) {
        createEmptyVector();
    }
 
    /**
     * 创建Vector
     * 调用链1（初始化）：
     *      {@link java.util.Vector#Vector(int)}
     *          传入capacity为2，如果为空，默认capacity是10
     *      {@link java.util.Vector#Vector(int, int)}
     *          第一个参数为初始容量initialCapacity，为2，第二个参数是capacityIncrement，默认在初始化时传0
     *          该操作创建一个2个长度的Object数组，且capacityIncrement赋值为0
     *          代码：
     *              super();
     *              if (initialCapacity < 0)
     *                  throw new IllegalArgumentException("Illegal Capacity: "+ initialCapacity);
     *              this.elementData = new Object[initialCapacity];
     *              this.capacityIncrement = capacityIncrement;
     * 调用链2：
     *      {@link java.util.Vector#add(Object)}
     *          synchronized修饰，modCount自增，计算容量（如果不够会执行扩容），根据下标为elementData赋值
     *          代码：
     *              modCount++;
     *              ensureCapacityHelper(elementCount + 1);  //elementCount=0,只需确保有一个空间可用
     *              elementData[elementCount++] = e;
     *              return true;
     *      {@link java.util.Vector#ensureCapacity(int)}
     *          这里传入的参数minCapacity为1，即我们需要的最小容量是1，elementData length是我们初始化时的容量2，此时不会扩容
     *              if (minCapacity - elementData.length > 0)
     *                  grow(minCapacity);
     *      elementData[elementCount++] = e; //因为无需扩容，直接根据数组下标赋值就好啦
     * 调用链3：
     *      此时和调用链2是一样的，不存在扩容
     * 调用链4：
     *      {@link java.util.Vector#add(Object)}
     *          synchronized修饰，modCount自增，计算容量（如果不够会执行扩容），根据下标为elementData赋值
     *          代码：
     *              modCount++;
     *              ensureCapacityHelper(elementCount + 1);  //elementCount=0,只需确保有一个空间可用
     *              elementData[elementCount++] = e;
     *              return true;
     *      {@link java.util.Vector#ensureCapacity(int)}
     *          这里传入的参数minCapacity为3，即我们需要的最小容量是3，elementData length是我们初始化时的容量2，此时会扩容
     *              if (minCapacity - elementData.length > 0)
     *                  grow(minCapacity);
     *      {@link java.util.Vector#grow(int)}
     *          计算新的容量，因为capacityIncrement初始化时是0，所以newCapacity = oldCapacity + oldCapacity，
     *          即新容量等于旧容量的2倍，elementData扩容为原来的2倍数，即4
     *          代码：
     *              int oldCapacity = elementData.length;
     *              int newCapacity = oldCapacity + ((capacityIncrement > 0) ? capacityIncrement : oldCapacity);
     *              if (newCapacity - minCapacity < 0)
     *                  newCapacity = minCapacity;
     *              if (newCapacity - MAX_ARRAY_SIZE > 0)
     *                  newCapacity = hugeCapacity(minCapacity); //MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8
     *              elementData = Arrays.copyOf(elementData, newCapacity);
     *      最后根据下标赋值就好啦
     *          代码：
     *              elementData[elementCount++] = e;
     * 调用链5：
     *      因为上一步扩容到4，此时不会扩容
     * 调用链6：
     *      此时因为容量4已用完，会触发扩容，容量扩大到8
     */
    private static void createEmptyVector(){
        Vector<String> vector = new Vector<>(2); //1
        vector.add("aaa"); //2
        vector.add("bbb"); //3
        vector.add("ccc"); //4
        vector.add("ddd"); //5
        vector.add("eee"); //6
        System.out.println(vector.toString());
    }
 
    /**
     * 这里初始化时指定每次扩容的大小，交给用户设置
     */
    private static void createEmptyVectorWithIncrement(){
        Vector<String> vector = new Vector<>(2, 3);//初始容量为2，每次扩容空间为3
        vector.add("aaa"); //不扩容，容量为2
        vector.add("bbb"); //不扩容，容量为2
        vector.add("ccc"); //扩容，扩容为5
        vector.add("ddd"); //不扩容，容量为5
        vector.add("eee"); //不扩容，容量为5
        System.out.println(vector.toString());
    }
}
```

扩容方法：

```
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
 
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

注意不同于ArrayList的扩容方式

ArrayList扩容公式为：newCapacity  =  oldCapacity + oldCapacity >> 1
