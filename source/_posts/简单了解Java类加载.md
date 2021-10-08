---
title: 简单了解Java类加载
date: 2018-09-30 14:20:57
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```java
package com.example.demo.test1;
 
import sun.misc.Launcher;
 
import java.io.File;
import java.util.StringTokenizer;
 
/**
 * <p>
 * <code>TestClassLoader</code>
 * </p>
 * Description: java类加载机制
 *
 * @author Chu Mingcheng
 * @date 2018/9/10
 */
public class TestClassLoader extends Launcher{
 
    public static void main(String[] args) {
        TestClassLoader test = new TestClassLoader();
        test.testAppClassLoader();
        test.testBootstrapLoader();
        test.test4();
    }
 
    /**
     * test AppClassLoader
     */
    private void testAppClassLoader(){
        System.out.println("\n----------AppClassLoader：应用（系统）类加载器------------");
        final String s = System.getProperty("java.class.path");
        System.out.println(s);
        final File[] path = (s == null) ? new File[0] : getClassPath(s);
        for(File f : path){
            System.out.println(f);
        }
    }
 
    /**
     * test ExtClassLoader
     */
    private void test4(){
        System.out.println("\n---------ExtClassLoader：扩展类加载器-------------");
        final String s = System.getProperty("java.ext.dirs");//对应路径
        System.out.println(s);
 
        File[] dirs;
        if (s != null) {
            StringTokenizer st =
                    new StringTokenizer(s, File.pathSeparator);
            int count = st.countTokens();
            dirs = new File[count];
            for (int i = 0; i < count; i++) {
                dirs[i] = new File(st.nextToken());
            }
        } else {
            dirs = new File[0];
        }
 
        for(File f:dirs){
            System.out.println(f.getAbsolutePath());
        }
    }
 
    /**
     * test BootClassLoader
     */
    private void testBootstrapLoader(){
        System.out.println("\n--------BootstrapLoader：启动类（根）加载器--------------");
        final String s = System.getProperty("sun.boot.class.path");
        System.out.println(s);
        final File[] path = (s == null) ? new File[0] : getClassPath(s);
        for(File f : path){
            System.out.println(f);
        }
        //sun.misc.Launcher launcher = sun.misc.Launcher.getLauncher();
        //System.out.println(launcher.getClass().getClassLoader());
    }
 
    /**
     * Launcher类私有方法，这里是直接复制一份过来的
     * @see sun.misc.Launcher#getClassPath
     * @param var0
     * @return
     */
    private static File[] getClassPath(String var0) {
        File[] var1;
        if (var0 != null) {
            int var2 = 0;
            int var3 = 1;
            boolean var4 = false;
 
            int var5;
            int var7;
            for(var5 = 0; (var7 = var0.indexOf(File.pathSeparator, var5)) != -1; var5 = var7 + 1) {
                ++var3;
            }
 
            var1 = new File[var3];
            var4 = false;
 
            for(var5 = 0; (var7 = var0.indexOf(File.pathSeparator, var5)) != -1; var5 = var7 + 1) {
                if (var7 - var5 > 0) {
                    var1[var2++] = new File(var0.substring(var5, var7));
                } else {
                    var1[var2++] = new File(".");
                }
            }
 
            if (var5 < var0.length()) {
                var1[var2++] = new File(var0.substring(var5));
            } else {
                var1[var2++] = new File(".");
            }
 
            if (var2 != var3) {
                File[] var6 = new File[var2];
                System.arraycopy(var1, 0, var6, 0, var2);
                var1 = var6;
            }
        } else {
            var1 = new File[0];
        }
 
        return var1;
    }
}
```

java中类的加载遵循双亲委派原则，如果你自己写一个java.lang.String，当我们在启动时，其实加载的是rt.jar中的String类，不会加载到我们自己写的String类，即你永远不会改变jdk自带的String类
