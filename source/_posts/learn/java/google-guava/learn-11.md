---
title: CharMatcher类
date: 2017-10-28 16:05:59
categories:
- [学习, Java语言学习, GoogleGuava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
处理字符串

# 程序示例
```java
package com.example.google.guava.demo.string;
 
import com.google.common.base.CharMatcher;
 
/**
 * <p>
 * <code>CharMatcherTest</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/23 12:14
 */
public class CharMatcherTest {
 
    public static void main(String[] args) {
        // 1.获取字符串中数字
        String str1 = "mahesh123ff87f";
        String result1 = CharMatcher.digit().retainFrom(str1);
        System.out.println("1.获取字符串中数字："+result1);
 
        // 2.把多个空格替换,并去掉首位的空格
        String str2 = "     Mahesh     Parashar  ";
        String result2 = CharMatcher.whitespace().trimAndCollapseFrom(str2, ' ');
        System.out.println("2.把多个空格替换,并去掉首位的空格"+result2);
 
        // 3.去掉转义字符(\t,\n,\b...)
        String str3 = " ab\tcd\nef\bg";
        String result3 = CharMatcher.javaIsoControl().removeFrom(str3);
        System.out.println("3.去掉转义字符"+result3);
 
        // 4.把所有的数字用"*"代替
        String str4 = "124abc85dds";
        String result4 = CharMatcher.javaDigit().replaceFrom(str4,"*");
        System.out.println("4.替换数字："+result4);
 
        // 5.获取所有的数字和小写字母
        String str5 = "124abc85ddsAF1HNsd";
        String result5 = CharMatcher.javaDigit().or(CharMatcher.javaLowerCase()).retainFrom(str5);
        System.out.println("5.获取所有的数字和小写字母："+result5);
 
        // 6.获取所有的大写字母
        String result6 = CharMatcher.javaUpperCase().retainFrom(str5);
        System.out.println("6.获取所有的大写字母："+result6);
 
        // 7.获取所有单字节长度的符号
        String str7 = ",dg,123AH中国";
        String result7 = CharMatcher.singleWidth().retainFrom(str7);
        System.out.println("7.获取所有单字节长度的符号："+result7);
 
        // 8.获取字母
        String str8 = "FirstName LastName +1 123 456 789 !@#$%^&*()_+|}{:\"?><";
        String result8 = CharMatcher.javaLetter().retainFrom(str8);
        System.out.println("8.获取字母："+result8);
 
 
        // 9.获取字母和数字
        String result9 = CharMatcher.javaLetterOrDigit().retainFrom(str8);
        System.out.println("9.获取字母和数字："+result9);
 
        // 10.出现次数统计
        Integer count10 = CharMatcher.any().countIn(str8);
        System.out.println("10.出现次数："+count10);
 
        // 11.数字出现次数
        Integer count11 = CharMatcher.digit().countIn(str8);;
        System.out.println("11.数字出现次数："+count11);
 
        // 12.获得除大写字母外其他所有字符
        String result12 = CharMatcher.javaLowerCase().negate().retainFrom(str8);
        System.out.println("12.获得除大写字母外其他所有字符："+result12);
 
//        CharMatcher本身提供了很多CharMatcher实现类,如下:
//        ANY: 匹配任何字符
//        ASCII: 匹配是否是ASCII字符
//        BREAKING_WHITESPACE: 匹配所有可换行的空白字符(不包括非换行空白字符,例如"\u00a0")
//        DIGIT: 匹配ASCII数字
//        INVISIBLE: 匹配所有看不见的字符
//        JAVA_DIGIT: 匹配UNICODE数字, 使用 Character.isDigit() 实现
//        JAVA_ISO_CONTROL: 匹配ISO控制字符, 使用 Charater.isISOControl() 实现
//        JAVA_LETTER: 匹配字母, 使用 Charater.isLetter() 实现
//        JAVA_LETTER_OR_DIGET: 匹配数字或字母
//        JAVA_LOWER_CASE: 匹配小写
//        JAVA_UPPER_CASE: 匹配大写
//        NONE: 不匹配所有字符
//        SINGLE_WIDTH: 匹配单字宽字符, 如中文字就是双字宽
//        WHITESPACE: 匹配所有空白字符
 
//        CharMatcher is(char match): 返回匹配指定字符的Matcher
//        CharMatcher isNot(char match): 返回不匹配指定字符的Matcher
//        CharMatcher anyOf(CharSequence sequence): 返回匹配sequence中任意字符的Matcher
//        CharMatcher noneOf(CharSequence sequence): 返回不匹配sequence中任何一个字符的Matcher
//        CharMatcher inRange(char startInclusive, char endIncludesive): 返回匹配范围内任意字符的Matcher
//        CharMatcher forPredicate(Predicate<? super Charater> predicate): 返回使用predicate的apply()判断匹配的Matcher
//        CharMatcher negate(): 返回以当前Matcher判断规则相反的Matcher
//        CharMatcher and(CharMatcher other): 返回与other匹配条件组合做与来判断的Matcher
//        CharMatcher or(CharMatcher other): 返回与other匹配条件组合做或来判断的Matcher
//        boolean matchesAnyOf(CharSequence sequence): 只要sequence中有任意字符能匹配Matcher,返回true
//        boolean matchesAllOf(CharSequence sequence): sequence中所有字符都能匹配Matcher,返回true
//        boolean matchesNoneOf(CharSequence sequence): sequence中所有字符都不能匹配Matcher,返回true
//        int indexIn(CharSequence sequence): 返回sequence中匹配到的第一个字符的坐标
//        int indexIn(CharSequence sequence, int start): 返回从start开始,在sequence中匹配到的第一个字符的坐标
//        int lastIndexIn(CharSequence sequence): 返回sequence中最后一次匹配到的字符的坐标
//        int countIn(CharSequence sequence): 返回sequence中匹配到的字符计数
//        String removeFrom(CharSequence sequence): 删除sequence中匹配到到的字符并返回
//        String retainFrom(CharSequence sequence): 保留sequence中匹配到的字符并返回
//        String replaceFrom(CharSequence sequence, char replacement): 替换sequence中匹配到的字符并返回
//        String trimFrom(CharSequence sequence): 删除首尾匹配到的字符并返回
//        String trimLeadingFrom(CharSequence sequence): 删除首部匹配到的字符
//        String trimTrailingFrom(CharSequence sequence): 删除尾部匹配到的字符
//        String collapseFrom(CharSequence sequence, char replacement): 将匹配到的组(连续匹配的字符)替换成replacement
//        String trimAndCollapseFrom(CharSequence sequence, char replacement): 先trim在replace
    }
}
```

# 测试结果
```
1.获取字符串中数字：12387
2.把多个空格替换,并去掉首位的空格Mahesh Parashar
3.去掉转义字符 abcdefg
4.替换数字：***abc**dds
5.获取所有的数字和小写字母：124abc85dds1sd
6.获取所有的大写字母：AFHN
7.获取所有单字节长度的符号：,dg,123AH
8.获取字母：FirstNameLastName
9.获取字母和数字：FirstNameLastName1123456789
10.出现次数：54
11.数字出现次数：10
12.获得除大写字母外其他所有字符：FN LN +1 123 456 789 !@#$%^&*()_+|}{:"?><
```

# 参考
http://www.yiibai.com/guava/guava_charmatcher.html

http://blog.csdn.net/victor_cindy1/article/details/52073155
