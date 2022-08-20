---
title: 简单了解java的HashMap扩容
date: 2019-08-26 16:12:20
tags:
- java
- HashMap
categories:
- [学习, Java语言学习, Java集合扩容]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
package com.example.demo.test;
 
import java.util.HashMap;
import java.util.Map;
 
/**
 * <p>
 * <code>HashMapTest</code>
 * </p>
 * Description: JDK 1.8
 * @see java.util.HashMap 本质上是Node<K,V>[]
 *
 * @author Jalen
 * @date 2019/8/26 14:03
 */
public class HashMapTest {
 
    public static void main(String[] args) {
        //createEmptyHashMap();
        createNonEmptyHashMap();
    }
 
    /**
     * @see java.util.HashMap#HashMap() loadFactor=DEFAULT_LOAD_FACTOR=0.75f
     * @see java.util.HashMap#put
     *      {@link java.util.HashMap#hash(Object key)}
     *      {@link java.lang.String#hashCode}
     *      {@link java.util.HashMap#putVal}
     *          {@link java.util.HashMap#resize}
     *              static final int DEFAULT_INITIAL_CAPACITY = 1 << 4    as newCapacity
     *              (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY) = 0.75f * 16 = 12    as newThreshold
     *              Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap]  创建16长度的kv节点
     *          {@link java.util.HashMap#newNode}
     *              create new Node<K,V> 赋值到newTab[0]
     *              modCount++
     *          {@link java.util.HashMap#afterNodeInsertion}
     */
    private static void createEmptyHashMap(){
        Map<String, String> map = new HashMap<>(); //loadFactor=DEFAULT_LOAD_FACTOR=0.75f
        map.put("a", "China");
        System.out.println(map.toString());
    }
 
    /**
     * @see java.util.HashMap#HashMap(int initialCapacity)
     *      this(initialCapacity, DEFAULT_LOAD_FACTOR);
     *      {@link java.util.HashMap#HashMap(int initialCapacity, float loadFactor)}
     *          check initialCapacity(不能小于0，如果大于MAXIMUM_CAPACITY则赋值为MAXIMUM_CAPACITY)，MAXIMUM_CAPACITY=1 << 30
     *          loadFactor设为DEFAULT_LOAD_FACTOR，即0.75f
     *          {@link java.util.HashMap#tableSizeFor(int cap)}
     *              通过initialCapacity计算得到threshold为8，此时map的capacity并未确定，在第一次put value时才会定下来
     * @see java.util.HashMap#put (第一次put值时会触发resize，重新计算threshold)
     *      {@link java.util.HashMap#hash(Object key)}
     *      {@link java.lang.String#hashCode}
     *      {@link java.util.HashMap#putVal}
     *          {@link java.util.HashMap#resize}
     *              (int)(DEFAULT_LOAD_FACTOR * initialCapacity) = 0.75f * 8 = 6    as newThreshold
     *              Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap]  创建8长度的kv节点
     *              (此时capacity为8，threshold重新计算为6)
     *          {@link java.util.HashMap#newNode}
     *              create new Node<K,V> 赋值到newTab[0]
     *              modCount++
     *          {@link java.util.HashMap#afterNodeInsertion}
     * @see java.util.HashMap#put
     *      {@link java.util.HashMap#hash(Object key)}
     *      {@link java.util.HashMap#putVal}
     *      {@link java.util.HashMap#afterNodeAccess}
     * @see java.util.HashMap#put
     *      {@link java.util.HashMap#hash(Object key)}
     *      {@link java.util.HashMap#putVal}
     *          {@link java.util.HashMap#newNode}
     *          {@link java.util.HashMap#afterNodeInsertion}
     * ... ...
     * @see java.util.HashMap#put
     *      {@link java.util.HashMap#hash(Object key)}
     *      {@link java.util.HashMap#putVal}
     *          {@link java.util.HashMap#newNode}
     *          {@link java.util.HashMap#resize}
     *              oldCap=8，oldThr=6；
     *              newCap=oldCap << 1，newThr=16*0.75=12
     *              Node<K, V> oldTab length=8(但实际上只有6个元素，2个空位), Node<K, V> newTab length=16(new Node[newCap])
     *              for循环将oldTab数据移动到newTab中，返回newTab
     *          {@link java.util.HashMap#afterNodeInsertion}
     */
    private static void createNonEmptyHashMap(){
        Map<String, String> map = new HashMap<>(5);
        map.put("1", "China");
        map.put("1", "America");
        map.put("2", "England");
        map.put("3", "France");
        map.put("4", "Japan");
        map.put("5", "Australia");
        map.put("6", "Canada");
        map.put("7", "Russia");  // 达到threshold，触发resize，oldCap=8，oldThr=6，newCap=oldCap << 1，newThr=16*0.75=12, 此时将oldTab数据移动到newTab里，返回newTab
        // ... ... 当添加到第13个元素时还会触发resize扩容，此时oldCap << 1=32，newThr = 24
        // ... ...
        System.out.println(map.toString());
    }
}
```

tableSizeFor方法

```
    /**
     * Returns a power of two size for the given target capacity.
     */
    static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```

resize方法

```
    /**
     * Initializes or doubles table size.  If null, allocates in
     * accord with initial capacity target held in field threshold.
     * Otherwise, because we are using power-of-two expansion, the
     * elements from each bin must either stay at same index, or move
     * with a power of two offset in the new table.
     *
     * @return the table
     */
    final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```




