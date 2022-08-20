---
title: java自带的排序工具
date: 2017-12-11 11:24:04
tags:
- java
- sort
categories:
- [学习, Java语言学习, Java]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```java
import java.util.Arrays;
import java.util.Collections;
import java.util.List;
 
/**
 * <p>
 * <code>TT</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/12/8 10:43
 */
public class TT {
 
    /**
     * 正序
     *
     * 利用jdk自带排序工具
     */
    private static void positiveSequence(){
        System.out.println("正序排列数字数组：");
        Integer[] intArr = new Integer[]{3,6,2,1,6,7,24,7};
        Arrays.sort(intArr);
        for (Integer in: intArr){
            System.out.print(in);
            System.out.print(",");
        }
 
        System.out.println("\n正序排列字符串数组：");
        String[] arr1 = new String[]{"f","2","c","e","3","d","1","a","b","back"};
        Arrays.sort(arr1);
        for (String str: arr1){
            System.out.print(str);
            System.out.print(",");
        }
 
        System.out.println("\n正序排列List集合：");
        String[] arr2 = new String[]{"f","2","c","e","3","d","1","a","b","back"};
        List list = Arrays.asList(arr2);
        Collections.sort(list);
        for (String str: arr2){
            System.out.print(str);
            System.out.print(",");
        }
        System.out.println();
    }
 
 
    /**
     * 反序
     *
     * 利用字符串反转
     */
    private static void reverse(){
        StringBuffer sb = new StringBuffer();
        String s = "a,b,c,d,e,f,g";
        sb.append(s);
        sb.reverse();
        System.out.println(sb.toString());
    }
 
    public static void main(String[] args) {
        System.out.println("正序");
        positiveSequence();
 
        System.out.println("\n反序");
        reverse();
    }
}
```

输出：

```
正序
正序排列数字数组：
1,2,3,6,6,7,7,24,
正序排列字符串数组：
1,2,3,a,b,back,c,d,e,f,
正序排列List集合：
1,2,3,a,b,back,c,d,e,f,
 
反序
g,f,e,d,c,b,a
```
