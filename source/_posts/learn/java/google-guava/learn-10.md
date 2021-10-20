---
title: Cache类（2）
date: 2017-10-27 16:05:58
categories:
- [学习, Java语言学习, Google Guava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
本地缓存

# 代码
两个类，一个测试类，一个缓存类

# 程序示例
```java
package com.example.google.guava.demo.cache;
 
import com.example.google.guava.demo.model.Department;
import com.google.common.cache.*;
 
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.concurrent.ExecutionException;
 
/**
 * <p>
 * <code>LoadingCacheTest</code>
 * </p>
 * Description:guava缓存工具,类似于使用java.util.concurrent.ConcurrentHashMap做缓存
 *
 * @author Mcchu
 * @date 2017/10/20 9:18
 */
public class LoadingCacheTest {
 
    private static SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
 
    private Department getDept(String id) throws ExecutionException {
        LoadingCache<String, Department> deptCache = LoadingCacheUtil.getLoadingCache();
        System.out.println("此缓存累积统计数据的当前快照："+deptCache.stats());
        System.out.println("缓存size：" + deptCache.size());
        return deptCache.get( id );
    }
 
    public static void main(String[] args) throws ExecutionException{
        LoadingCacheTest cacheTest = new LoadingCacheTest();
 
        try {
            // 1.第一次访问部门，会调用getDepartmentById()方法
            System.out.println( dateFormat.format( new Date() ) );
            String deptName1 = cacheTest.getDept("102").getDepartmentName();
            System.out.println("第一次访问："+deptName1);
 
 
            // 线程停滞一段时间过后，测试缓存是否存在
            Thread.sleep(10000); // 当前设置缓存写入后5s过期，如果线程停滞10s，需要重新读取数据源
 
 
            // 2.第二次访问，将不再调用getDepartmentById()方法，而是去缓存获取
            System.out.println( dateFormat.format( new Date() ) );
            String deptName2 = cacheTest.getDept("102").getDepartmentName();
            System.out.println("第二次访问："+deptName2);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
    }
 
}
```

```java
package com.example.google.guava.demo.cache;
 
import com.example.google.guava.demo.model.Department;
import com.google.common.cache.CacheBuilder;
import com.google.common.cache.CacheLoader;
import com.google.common.cache.LoadingCache;
 
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;
 
/**
 * <p>
 * <code>LoadingCacheUtil</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/20 15:04
 */
public class LoadingCacheUtil {
 
    private static LoadingCache<String, Department> departmentCache;
 
    static {
        CacheLoader<String,Department> deptCacheLoader = new CacheLoader<String, Department>() {
            @Override
            public Department load(String key) throws Exception {
                return getDepartmentById( key );
            }
        };
 
        departmentCache = CacheBuilder.newBuilder()
                .maximumSize(100)
                .expireAfterWrite(5L, TimeUnit.SECONDS)  //设置写入5s后过期
                .build(deptCacheLoader);
    }
 
    public static LoadingCache<String, Department> getLoadingCache() {
        return departmentCache;
    }
 
    /**
     * 部门假数据（实际来源可能是数据库或远程接口）
     * @return 部门列表
     */
    private static Department getDepartmentById(String id ){
        System.out.println("访问数据源");
        List<Department> departmentList = new ArrayList<>();
        Department department1 = new Department("101","采购部","1");
        Department department2 = new Department("102","财务部","1");
        Department department3 = new Department("103","信管部","1");
        Department department4 = new Department("104","外贸部","1");
        departmentList.add(department1);
        departmentList.add(department2);
        departmentList.add(department3);
        departmentList.add(department4);
 
        for ( Department department:departmentList ){
            String deptId = department.getDepartmentId();
            if ( id.equals(deptId) ){
                return department;
            }
        }
        return null;
    }
}
```

# 测试结果
1. 当线程停滞小于5s时结果
```
2017-10-20 15:32:18
此缓存累积统计数据的当前快照：CacheStats{hitCount=0, missCount=0, loadSuccessCount=0, loadExceptionCount=0, totalLoadTime=0, evictionCount=0}
缓存size：0
访问数据源
第一次访问：财务部
2017-10-20 15:32:22
此缓存累积统计数据的当前快照：CacheStats{hitCount=0, missCount=0, loadSuccessCount=0, loadExceptionCount=0, totalLoadTime=0, evictionCount=0}
缓存size：1
第二次访问：财务部
```

2. 当线程停滞大于5s时结果
```
2017-10-20 15:33:14
此缓存累积统计数据的当前快照：CacheStats{hitCount=0, missCount=0, loadSuccessCount=0, loadExceptionCount=0, totalLoadTime=0, evictionCount=0}
缓存size：0
访问数据源
第一次访问：财务部
2017-10-20 15:33:24
此缓存累积统计数据的当前快照：CacheStats{hitCount=0, missCount=0, loadSuccessCount=0, loadExceptionCount=0, totalLoadTime=0, evictionCount=0}
缓存size：1
访问数据源
第二次访问：财务部
```

可以发现当停滞时间大于5s时需要重新获取数据源，说明缓存设置是生效的。

# 参考
https://www.concretepage.com/google-api/google-guava-cache-example-using-loadingcache-cachebuilder-and-cacheloader
