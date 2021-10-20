---
title: Bimap接口
date: 2017-10-24 16:05:55
categories:
- [学习, Java语言学习, Google Guava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
扩展map操作

# 程序示例
```java
package com.example.google.guava.demo.collection;
 
import com.google.common.collect.BiMap;
import com.google.common.collect.HashBiMap;
 
import java.util.HashMap;
import java.util.Map;
import java.util.Set;
 
/**
 * <p>
 * <code>BimapTest</code>
 * </p>
 * Description:
 * 1.Bimap的值唯一；
 *
 * @author Mcchu
 * @date 2017/10/19 16:45
 */
public class BimapTest {
 
    public static void main(String[] args) {
        BiMap<Integer, String> idMap = HashBiMap.create();
        idMap.put(101, "Mahesh");
        idMap.put(102, "Sohan");
        idMap.put(103, "Ramesh");
 
        // 1.关联键的值
        Integer maheshId = idMap.inverse().get("Mahesh");
        System.out.println("1.名字为Mehesh的id："+maheshId);
 
        // 2.空值
        idMap.put(null,"Jack");
        idMap.put(null,"Kite"); //BiMap继承Map，固键唯一，值覆盖
        idMap.put(104,null);
        //idMap.put(105,null);  //抛java.lang.IllegalArgumentException: value already present: null
 
        Set<String> values = idMap.values();
        System.out.print("2.输出所有值：");
        for ( String value:values ){
            System.out.print(value);
            System.out.print(",");
        }
        System.out.println();
 
        // 3.包容另一个map
        Map<Integer,String> anotherIdMap = new HashMap<>();
        anotherIdMap.put(105,"Jermy");
        anotherIdMap.put(106,"Mercy");
        anotherIdMap.put(107,"Abely");
        anotherIdMap.put(108,"Jalen");
        idMap.putAll(anotherIdMap);
        System.out.println("3.输出添加新Map过后的所有值：");
        for ( Integer id:idMap.keySet() ){
            System.out.println( "  key："+ id + ", value：" + idMap.get(id) );
        }
        System.out.println();
 
        // 4.强制添加元素
        idMap.forcePut(109,null); //强制添加null，不会因已存在null而抛异常，但会覆盖，Bimap始终允许一个值为null
        Set<String> values1 = idMap.values();
        System.out.print("4.输出所有值：");
        for ( String value:values1 ){
            System.out.print(value);
            System.out.print(",");
        }
        System.out.println();
    }
}
```

# 输出结果
```
1.名字为Mehesh的id：101
2.输出所有值：Mahesh,Sohan,Ramesh,Kite,null,
3.输出添加新Map过后的所有值：
  key：101, value：Mahesh
  key：102, value：Sohan
  key：103, value：Ramesh
  key：null, value：Kite
  key：104, value：null
  key：105, value：Jermy
  key：106, value：Mercy
  key：107, value：Abely
  key：108, value：Jalen
 
4.输出所有键值：  key：101, value：Mahesh
  key：102, value：Sohan
  key：103, value：Ramesh
  key：null, value：Kite
  key：105, value：Jermy
  key：106, value：Mercy
  key：107, value：Abely
  key：108, value：Jalen
  key：109, value：null
```

# 参考
http://www.yiibai.com/guava/guava_multiset.html

# 接口源码参考
```java
package com.google.common.collect;
 
import com.google.common.annotations.GwtCompatible;
import com.google.errorprone.annotations.CanIgnoreReturnValue;
import java.util.Map;
import java.util.Set;
import javax.annotation.Nullable;
 
/**
 * A bimap (or "bidirectional map") is a map that preserves the uniqueness of
 * its values as well as that of its keys. This constraint enables bimaps to
 * support an "inverse view", which is another bimap containing the same entries
 * as this bimap but with reversed keys and values.
 *
 * <p>See the Guava User Guide article on <a href=
 * "https://github.com/google/guava/wiki/NewCollectionTypesExplained#bimap">
 * {@code BiMap}</a>.
 *
 * @author Kevin Bourrillion
 * @since 2.0
 */
@GwtCompatible
public interface BiMap<K, V> extends Map<K, V> {
  // Modification Operations
 
  /**
   * {@inheritDoc}
   *
   * @throws IllegalArgumentException if the given value is already bound to a
   *     different key in this bimap. The bimap will remain unmodified in this
   *     event. To avoid this exception, call {@link #forcePut} instead.
   */
  @CanIgnoreReturnValue
  @Override
  @Nullable
  V put(@Nullable K key, @Nullable V value);
 
  /**
   * An alternate form of {@code put} that silently removes any existing entry
   * with the value {@code value} before proceeding with the {@link #put}
   * operation. If the bimap previously contained the provided key-value
   * mapping, this method has no effect.
   *
   * <p>Note that a successful call to this method could cause the size of the
   * bimap to increase by one, stay the same, or even decrease by one.
   *
   * <p><b>Warning:</b> If an existing entry with this value is removed, the key
   * for that entry is discarded and not returned.
   *
   * @param key the key with which the specified value is to be associated
   * @param value the value to be associated with the specified key
   * @return the value which was previously associated with the key, which may
   *     be {@code null}, or {@code null} if there was no previous entry
   */
  @CanIgnoreReturnValue
  @Nullable
  V forcePut(@Nullable K key, @Nullable V value);
 
  // Bulk Operations
 
  /**
   * {@inheritDoc}
   *
   * <p><b>Warning:</b> the results of calling this method may vary depending on
   * the iteration order of {@code map}.
   *
   * @throws IllegalArgumentException if an attempt to {@code put} any
   *     entry fails. Note that some map entries may have been added to the
   *     bimap before the exception was thrown.
   */
  @Override
  void putAll(Map<? extends K, ? extends V> map);
 
  // Views
 
  /**
   * {@inheritDoc}
   *
   * <p>Because a bimap has unique values, this method returns a {@link Set},
   * instead of the {@link java.util.Collection} specified in the {@link Map}
   * interface.
   */
  @Override
  Set<V> values();
 
  /**
   * Returns the inverse view of this bimap, which maps each of this bimap's
   * values to its associated key. The two bimaps are backed by the same data;
   * any changes to one will appear in the other.
   *
   * <p><b>Note:</b>There is no guaranteed correspondence between the iteration
   * order of a bimap and that of its inverse.
   *
   * @return the inverse view of this bimap
   */
  BiMap<V, K> inverse();
}
```
