---
title: java其它编码规范
date: 2017-09-14 08:36:05
tags:
- principle
categories:
- [学习, Java语言学习, Java开发规范]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 【强制】在使用正则表达式时，利用好其预编译功能，可以有效加快正则匹配速度。
   说明： 不要在方法体内定义： Pattern pattern = Pattern.compile(规则);

2. 【强制】避免用 Apache Beanutils 进行属性的 copy。
   说明： Apache BeanUtils 性能较差，可以使用其他方案比如 Spring BeanUtils, Cglib
   BeanCopier。
   （可以使用modelmapper）

3. 【强制】 velocity 调用 POJO 类的属性时，建议直接使用属性名取值即可，模板引擎会自动按
   规范调用 POJO 的 getXxx()，如果是 boolean 基本数据类型变量（注意， boolean 命名不需要
   加 is 前缀），会自动调用 isXxx()方法。
   说明： 注意如果是 Boolean 包装类对象，优先调用 getXxx()的方法。

4. 【强制】后台输送给页面的变量必须加`$!{var}` - 中间的感叹号。
   说明： 如果 var=null 或者不存在，那么`${var}`会直接显示在页面上。

5. 【强制】注意 Math.random() 这个方法返回是 double 类型，注意取值范围 0≤x<1（能够取
   到零值，注意除零异常），如果想获取整数类型的随机数，不要将 x 放大 10 的若干倍然后取
   整，直接使用 Random 对象的 nextInt 或者 nextLong 方法。

6. 【强制】获取当前毫秒数： System.currentTimeMillis(); 而不是 new Date().getTime();
   说明： 如果想获取更加精确的纳秒级时间值，用 System.nanoTime。在 JDK8 中，针对统计时
   间等场景，推荐使用 Instant 类。

7. 【推荐】尽量不要在 vm 中加入变量声明、逻辑运算符，更不要在 vm 模板中加入任何复杂的逻
   辑。

8. 【推荐】任何数据结构的使用都应限制大小。
   说明： 这点很难完全做到，但很多次的故障都是因为数据结构自增长，结果造成内存被吃光。

9. 【推荐】对于“ 明确停止使用的代码和配置” ，如方法、变量、类、配置文件、动态配置属性
   等要坚决从程序中清理出去，避免造成过多垃圾。清理这类垃圾代码是技术气场，不要有这样
   的观念： “ 不做不错，多做多错” 。
