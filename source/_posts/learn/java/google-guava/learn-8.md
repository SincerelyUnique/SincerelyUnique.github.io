---
title: Table接口
date: 2017-10-25 16:05:56
categories:
- [学习, Java语言学习, Google Guava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
简化表格操作，形似Excel的单元格操作；

# 程序示例
```java
package com.example.google.guava.demo.collection;
 
import com.google.common.collect.HashBasedTable;
import com.google.common.collect.Table;
 
import java.util.Collection;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;
 
/**
 * <p>
 * <code>TableTest</code>
 * </p>
 * Description: Table<R, C, V>接口类似Map<R,Map<C,V>>，R-row,C-column,V-value
 *
 * @author Mcchu
 * @date 2017/10/20 8:25
 */
public class TableTest {
 
    public static void main(String[] args) {
        // 1.创建表格Table
        Table<String, String, String> employeeTable = HashBasedTable.create();
 
        // 2.初始化表格Table,参数分别对应表格的行、列、值
        employeeTable.put("IBM", "101","Mahesh");
        employeeTable.put("IBM", "102","Ramesh");
        employeeTable.put("IBM", "103","Suresh");
 
        employeeTable.put("Microsoft", "111","Sohan");
        employeeTable.put("Microsoft", "112","Mohan");
        employeeTable.put("Microsoft", "113","Rohan");
 
        employeeTable.put("TCS", "121","Ram");
        employeeTable.put("TCS", "122","Shyam");
        employeeTable.put("TCS", "123","Sunil");
 
        // 3.获取表格中某个单元格的值
        String IBM103name = employeeTable.get("IBM","103");
        System.out.println("3.行key为IBM列key为103的员工值："+IBM103name);
 
        // 4.转换为Map，将Table<R,C,V>转换成Map<C,V>形势
        Map<String,String> ibmEmployees =  employeeTable.row("IBM");
        System.out.println("4.IBM员工信息：");
        for (Map.Entry<String,String> entry:ibmEmployees.entrySet()){
            String id = entry.getKey();
            String name = entry.getValue();
            System.out.println("  员工id："+ id +",员工姓名："+name);
        }
 
        // 5.键去重（即获取唯一）
        Set<String> employeeRowKeys = employeeTable.rowKeySet();
        System.out.println("5.获取表中所有唯一键：");
        for (String rowKey: employeeRowKeys){
            System.out.println("  行号："+rowKey);
        }
 
        // 6.转换为Map，将Table<R,C,V>转换成Map<R,V>形式
        Map<String,String> employerMap1 =  employeeTable.column("102");
        System.out.println("6.列102的数据：");
        for ( Map.Entry<String,String> entry:employerMap1.entrySet() ){
            String company = entry.getKey();
            String name = entry.getValue();
            System.out.println("  公司名字："+company+",员工姓名"+name);
        }
 
        // 7.遍历表格
        Set<Table.Cell<String,String,String>> tableSet = employeeTable.cellSet();
        Iterator<Table.Cell<String,String,String>> it = tableSet.iterator();
        System.out.println("7.遍历Table：");
        while (it.hasNext()){
            Table.Cell<String,String,String> data = it.next();
            String rowCompany = data.getRowKey();
            String columnId = data.getColumnKey();
            String valueName = data.getValue();
            System.out.println("  行号："+rowCompany+",列号："+columnId+",值："+valueName);
        }
 
        // 8.获取列键
        System.out.println("8.获取所有列键：");
        for (String columnKey : employeeTable.columnKeySet()){
            Map<String,String> rowAndValue = employeeTable.column(columnKey);
            System.out.println("  列："+columnKey+"对应的数据Map是："+rowAndValue);
        }
 
        // 9.
        Collection<String> values = employeeTable.values();
        System.out.println("9.获取所有值："+values.toString());
 
        // 10.其他简单方法，不做尝试
        //boolean contains(Object rowKey, Object columnKey)
        //boolean containsColumn(Object columnKey)
        //boolean containsRow(Object rowKey)
        //boolean containsValue(Object value)
        //boolean equals(Object obj)
        //int hashCode()
        //boolean isEmpty()
        //void putAll(Table<? extends R,? extends C,? extends V> table)
        //V remove(Object rowKey, Object columnKey)
        //int size()
    }
}
```

# 输出结果
```
3.行key为IBM列key为103的员工值：Suresh
4.IBM员工信息：
  员工id：101,员工姓名：Mahesh
  员工id：102,员工姓名：Ramesh
  员工id：103,员工姓名：Suresh
5.获取表中所有唯一键：
  行号：IBM
  行号：Microsoft
  行号：TCS
6.列102的数据：
  公司名字：IBM,员工姓名Ramesh
7.遍历Table：
  行号：IBM,列号：101,值：Mahesh
  行号：IBM,列号：102,值：Ramesh
  行号：IBM,列号：103,值：Suresh
  行号：Microsoft,列号：111,值：Sohan
  行号：Microsoft,列号：112,值：Mohan
  行号：Microsoft,列号：113,值：Rohan
  行号：TCS,列号：121,值：Ram
  行号：TCS,列号：122,值：Shyam
  行号：TCS,列号：123,值：Sunil
8.获取所有列键：
  列：101对应的数据Map是：{IBM=Mahesh}
  列：102对应的数据Map是：{IBM=Ramesh}
  列：103对应的数据Map是：{IBM=Suresh}
  列：111对应的数据Map是：{Microsoft=Sohan}
  列：112对应的数据Map是：{Microsoft=Mohan}
  列：113对应的数据Map是：{Microsoft=Rohan}
  列：121对应的数据Map是：{TCS=Ram}
  列：122对应的数据Map是：{TCS=Shyam}
  列：123对应的数据Map是：{TCS=Sunil}
9.获取所有值：[Mahesh, Ramesh, Suresh, Sohan, Mohan, Rohan, Ram, Shyam, Sunil]
```

# 参考
http://www.yiibai.com/guava/guava_table.html

# 接口源码参考
```java
package com.google.common.collect;
 
import com.google.common.annotations.GwtCompatible;
import com.google.common.base.Objects;
import com.google.errorprone.annotations.CanIgnoreReturnValue;
import com.google.errorprone.annotations.CompatibleWith;
import java.util.Collection;
import java.util.Map;
import java.util.Set;
import javax.annotation.Nullable;
 
/**
 * A collection that associates an ordered pair of keys, called a row key and a
 * column key, with a single value. A table may be sparse, with only a small
 * fraction of row key / column key pairs possessing a corresponding value.
 *
 * <p>The mappings corresponding to a given row key may be viewed as a {@link
 * Map} whose keys are the columns. The reverse is also available, associating a
 * column with a row key / value map. Note that, in some implementations, data
 * access by column key may have fewer supported operations or worse performance
 * than data access by row key.
 *
 * <p>The methods returning collections or maps always return views of the
 * underlying table. Updating the table can change the contents of those
 * collections, and updating the collections will change the table.
 *
 * <p>All methods that modify the table are optional, and the views returned by
 * the table may or may not be modifiable. When modification isn't supported,
 * those methods will throw an {@link UnsupportedOperationException}.
 *
 * <p>See the Guava User Guide article on <a href=
 * "https://github.com/google/guava/wiki/NewCollectionTypesExplained#table">
 * {@code Table}</a>.
 *
 * @author Jared Levy
 * @param <R> the type of the table row keys
 * @param <C> the type of the table column keys
 * @param <V> the type of the mapped values
 * @since 7.0
 */
@GwtCompatible
public interface Table<R, C, V> {
  // TODO(jlevy): Consider adding methods similar to ConcurrentMap methods.
 
  // Accessors
 
  /**
   * Returns {@code true} if the table contains a mapping with the specified row and column keys.
   *
   * @param rowKey key of row to search for
   * @param columnKey key of column to search for
   */
  boolean contains(
      @Nullable @CompatibleWith("R") Object rowKey,
      @Nullable @CompatibleWith("C") Object columnKey);
 
  /**
   * Returns {@code true} if the table contains a mapping with the specified row key.
   *
   * @param rowKey key of row to search for
   */
  boolean containsRow(@Nullable @CompatibleWith("R") Object rowKey);
 
  /**
   * Returns {@code true} if the table contains a mapping with the specified column.
   *
   * @param columnKey key of column to search for
   */
  boolean containsColumn(@Nullable @CompatibleWith("C") Object columnKey);
 
  /**
   * Returns {@code true} if the table contains a mapping with the specified value.
   *
   * @param value value to search for
   */
  boolean containsValue(@Nullable @CompatibleWith("V") Object value);
 
  /**
   * Returns the value corresponding to the given row and column keys, or {@code null} if no such
   * mapping exists.
   *
   * @param rowKey key of row to search for
   * @param columnKey key of column to search for
   */
  V get(
      @Nullable @CompatibleWith("R") Object rowKey,
      @Nullable @CompatibleWith("C") Object columnKey);
 
  /** Returns {@code true} if the table contains no mappings. */
  boolean isEmpty();
 
  /**
   * Returns the number of row key / column key / value mappings in the table.
   */
  int size();
 
  /**
   * Compares the specified object with this table for equality. Two tables are
   * equal when their cell views, as returned by {@link #cellSet}, are equal.
   */
  @Override
  boolean equals(@Nullable Object obj);
 
  /**
   * Returns the hash code for this table. The hash code of a table is defined
   * as the hash code of its cell view, as returned by {@link #cellSet}.
   */
  @Override
  int hashCode();
 
  // Mutators
 
  /** Removes all mappings from the table. */
  void clear();
 
  /**
   * Associates the specified value with the specified keys. If the table
   * already contained a mapping for those keys, the old value is replaced with
   * the specified value.
   *
   * @param rowKey row key that the value should be associated with
   * @param columnKey column key that the value should be associated with
   * @param value value to be associated with the specified keys
   * @return the value previously associated with the keys, or {@code null} if
   *     no mapping existed for the keys
   */
  @CanIgnoreReturnValue
  @Nullable
  V put(R rowKey, C columnKey, V value);
 
  /**
   * Copies all mappings from the specified table to this table. The effect is
   * equivalent to calling {@link #put} with each row key / column key / value
   * mapping in {@code table}.
   *
   * @param table the table to add to this table
   */
  void putAll(Table<? extends R, ? extends C, ? extends V> table);
 
  /**
   * Removes the mapping, if any, associated with the given keys.
   *
   * @param rowKey row key of mapping to be removed
   * @param columnKey column key of mapping to be removed
   * @return the value previously associated with the keys, or {@code null} if no such value existed
   */
  @CanIgnoreReturnValue
  @Nullable
  V remove(
      @Nullable @CompatibleWith("R") Object rowKey,
      @Nullable @CompatibleWith("C") Object columnKey);
 
  // Views
 
  /**
   * Returns a view of all mappings that have the given row key. For each row
   * key / column key / value mapping in the table with that row key, the
   * returned map associates the column key with the value. If no mappings in
   * the table have the provided row key, an empty map is returned.
   *
   * <p>Changes to the returned map will update the underlying table, and vice
   * versa.
   *
   * @param rowKey key of row to search for in the table
   * @return the corresponding map from column keys to values
   */
  Map<C, V> row(R rowKey);
 
  /**
   * Returns a view of all mappings that have the given column key. For each row
   * key / column key / value mapping in the table with that column key, the
   * returned map associates the row key with the value. If no mappings in the
   * table have the provided column key, an empty map is returned.
   *
   * <p>Changes to the returned map will update the underlying table, and vice
   * versa.
   *
   * @param columnKey key of column to search for in the table
   * @return the corresponding map from row keys to values
   */
  Map<R, V> column(C columnKey);
 
  /**
   * Returns a set of all row key / column key / value triplets. Changes to the
   * returned set will update the underlying table, and vice versa. The cell set
   * does not support the {@code add} or {@code addAll} methods.
   *
   * @return set of table cells consisting of row key / column key / value
   *     triplets
   */
  Set<Cell<R, C, V>> cellSet();
 
  /**
   * Returns a set of row keys that have one or more values in the table.
   * Changes to the set will update the underlying table, and vice versa.
   *
   * @return set of row keys
   */
  Set<R> rowKeySet();
 
  /**
   * Returns a set of column keys that have one or more values in the table.
   * Changes to the set will update the underlying table, and vice versa.
   *
   * @return set of column keys
   */
  Set<C> columnKeySet();
 
  /**
   * Returns a collection of all values, which may contain duplicates. Changes
   * to the returned collection will update the underlying table, and vice
   * versa.
   *
   * @return collection of values
   */
  Collection<V> values();
 
  /**
   * Returns a view that associates each row key with the corresponding map from
   * column keys to values. Changes to the returned map will update this table.
   * The returned map does not support {@code put()} or {@code putAll()}, or
   * {@code setValue()} on its entries.
   *
   * <p>In contrast, the maps returned by {@code rowMap().get()} have the same
   * behavior as those returned by {@link #row}. Those maps may support {@code
   * setValue()}, {@code put()}, and {@code putAll()}.
   *
   * @return a map view from each row key to a secondary map from column keys to
   *     values
   */
  Map<R, Map<C, V>> rowMap();
 
  /**
   * Returns a view that associates each column key with the corresponding map
   * from row keys to values. Changes to the returned map will update this
   * table. The returned map does not support {@code put()} or {@code putAll()},
   * or {@code setValue()} on its entries.
   *
   * <p>In contrast, the maps returned by {@code columnMap().get()} have the
   * same behavior as those returned by {@link #column}. Those maps may support
   * {@code setValue()}, {@code put()}, and {@code putAll()}.
   *
   * @return a map view from each column key to a secondary map from row keys to
   *     values
   */
  Map<C, Map<R, V>> columnMap();
 
  /**
   * Row key / column key / value triplet corresponding to a mapping in a table.
   *
   * @since 7.0
   */
  interface Cell<R, C, V> {
    /**
     * Returns the row key of this cell.
     */
    @Nullable
    R getRowKey();
 
    /**
     * Returns the column key of this cell.
     */
    @Nullable
    C getColumnKey();
 
    /**
     * Returns the value of this cell.
     */
    @Nullable
    V getValue();
 
    /**
     * Compares the specified object with this cell for equality. Two cells are
     * equal when they have equal row keys, column keys, and values.
     */
    @Override
    boolean equals(@Nullable Object obj);
 
    /**
     * Returns the hash code of this cell.
     *
     * <p>The hash code of a table cell is equal to {@link
     * Objects#hashCode}{@code (e.getRowKey(), e.getColumnKey(), e.getValue())}.
     */
    @Override
    int hashCode();
  }
}
```
