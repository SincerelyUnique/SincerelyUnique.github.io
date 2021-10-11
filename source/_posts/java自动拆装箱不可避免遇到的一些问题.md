---
title: java自动拆装箱不可避免遇到的一些问题
date: 2017-12-07 11:26:49
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

在《java特种兵》一书，胖哥举例说明了Integer的拆装箱问题，以及jdk自己做了一些优化的坑，同时也提出了一些其他原始类型的拆装箱常见的操作风险，这里记录一下。

首先列出一个坑：

```java
/**
 * <p>
 * <code>Test</code>
 * </p>
 * Description: 《java特种兵》书中代码
 *
 * @author Mcchu
 * @date 2017/12/5 15:57
 */
public class Test {
 
    /**
     * 自动拆装箱的坑
     * 使用Integer时，需注意下面这个坑
     *
     * 这里定义的是Integer对象，而等号后的数值其实是原始类型的，会隐式地使用Integer.valueOf(int)做一步转换；
     * 原始类型不属于对象；
     * 原始类型包括boolean、byte、short、char、int、float、long、double这8种常见的数据类型（Primitive）
     *
     * 下面的现象在低版本jdk中不存在，在高版本（从1.5开始）是jdk的一个坑，因为使用了IntegerCache
     * 设计目的：通过缓存经常请求的值，可能会产生更好的空间和时间性能。
     * （jdk自己说：as this method is likely to yield significantly better space and
     * time performance by caching frequently requested values.）
     *
     * 注意这个IntegerCache缓存的最大值可以通过jvm启动参数设置，如：-Djava.lang.Integer.IntegerCache.high=200
     */
    private static void test1(){
        Integer a = 1;
        Integer b = 1;
        Integer c = 200;
        Integer d = 200;
        System.out.println(a == b);//true，对于同一个数字 cache 的对象是同一块内存地址
        System.out.println(c == d);//false，重新 new Integer(int)，开辟新的地址
    }
 
    public static void main(String[] args) {
        test1();
    }
}
```

上面的输出结果是：

```
true
false
```

结果很怪，不应该相等吗？

其实如果看一下jdk源码，就可以知道一些了

```java
/**
     * Returns an {@code Integer} instance representing the specified
     * {@code int} value.  If a new {@code Integer} instance is not
     * required, this method should generally be used in preference to
     * the constructor {@link #Integer(int)}, as this method is likely
     * to yield significantly better space and time performance by
     * caching frequently requested values.
     *
     * This method will always cache values in the range -128 to 127,
     * inclusive, and may cache other values outside of this range.
     *
     * @param  i an {@code int} value.
     * @return an {@code Integer} instance representing {@code i}.
     * @since  1.5
     */
    public static Integer valueOf(int i) {
        assert IntegerCache.high >= 127;
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```

因为缓存的影响

类似的风险及需要注意的地方：

- Boolean 的两个值 true 和 false 都是 cache 在内存中的，无须做任何改造，自己 new Boolean 是另外一块空间。
- Byte 的 256 个值全部 cache 在内存中，和自己 new Byte 操作得到的对象不是一块空间。
- Short、Long 两种类型的 cache 范围为-128～127，无法调整最大尺寸，即没有设置，代码中完全写死，如果要扩展，需自己来做。
- Float、Double 没有 cache，要在实际场景中 cache 需自己操作，例如，在做图纸尺寸时可以将每种常见尺寸记录在内存中。
- 当 Integer 与 int 类型进行比较的时候，会将 Integer 转化为 int 类型来比较（也就是通过调用 intValue()方法返回数字），直接比较数字，在这种情况下是不会出现例子中的问题的。
- Integer 做“>”、“>=”、“<”、“<=”比较的时候，Integer 会自动拆箱，就是比较它们的数字值。
- switch case 为选择语句，匹配的时候不会用 equals()，而是直接用“==”。而在 switchcase 语句中，语法层面 case 部分是不能写 Integer 对象的，只能是普通的数字，如果是普通的数字就会将传入的 Integer 自动拆箱，所以它也不会出现例子中的情况。
- 在 JDK 1.7 中，支持对 String 对象的 switch case 操作，这其实是语法糖，在编译后的字节码中依然是 if else 语句，并且是通过 equals()实现的。
- 在反射当中，对于 Integer 属性不能使用 field.setInt()和 field.getInt()操作。在本书的src/chapter01/AutoBoxReflect.java 中用例子来说明。

（以上全部来自《java特种兵》）
