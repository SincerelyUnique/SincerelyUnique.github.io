---
title: Cache类（1）
date: 2017-10-26 16:05:57
categories:
- [学习, Java语言学习, Google Guava]
tags:
- java
- guava
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 功能
简单实现数据的缓存，提供了一些基本的缓存特性设置（如过期时间等）

# demo描述
为3张表数据做缓存：公司表、部门表、员工表，使用时通过不同的键值调用此缓存；代码中涉及到多线程访问缓存，可以自己切换线程，更好说明一些；

# 程序示例
```java
package com.example.google.guava.demo.cache;
 
import com.example.google.guava.demo.model.Company;
import com.example.google.guava.demo.model.Department;
import com.example.google.guava.demo.model.Employee;
import com.google.common.cache.Cache;
import com.google.common.cache.CacheBuilder;
 
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
 
/**
 * <p>
 * <code>CacheTest</code>
 * </p>
 * Description:以员工表、部门表、公司表三张表的缓存为例
 *
 * @author Mcchu
 * @date 2017/10/20 9:34
 */
public class CacheTest<K,V> {
 
    private Cache<K, V> cache= CacheBuilder
            .newBuilder()
            .maximumSize(2)
            .expireAfterWrite(10, TimeUnit.MINUTES)
            .build();
 
 
    @SuppressWarnings("unchecked")
    private Object getCache(K key,final String threadName){
        Object value=null;
 
        try {
            System.out.println("线程："+threadName+"获取数据");
 
            // 根据不同的key获取缓存数据
            if ( "company".equals(key) ){
                Callable callable = ()->{
                    System.out.println("###获取公司缓存数据###");
                    return (List<Company>) getCompanyCacheList();
                };
                value = cache.get(key,callable);
            }
            if ( "department".equals(key) ){
                Callable callable = ()->{
                    System.out.println("###获取部门缓存数据###");
                    return (List<Department>) getDepartmentCacheList();
                };
                value = cache.get(key,callable);
            }
            if ( "employee".equals(key) ){
                Callable callable = ()->{
                    System.out.println("###获取人员缓存数据###");
                    return (List<Employee>) getEmployeeCacheList();
                };
                value = cache.get(key,callable);
            }
 
            // 下面是java7
            /*value = cache.get(key, new Callable<V>() {
                public V call() {
                    System.out.println("###获取缓存数据###");
                    return (V) "dataValue";
                }
            });*/
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
 
        return value;
    }
 
 
    public static void main(String[] args) {
        final CacheTest<String,String> cacheTest=new CacheTest<String,String>();
 
        Runnable task1 = () -> {
            System.out.println("线程t1开始");
            Object value=cacheTest.getCache("company","T1");
            System.out.println("线程t1获取到的缓存值："+value);
            System.out.println("线程t1开始");
        };
        new Thread(task1).start();
 
        Runnable task2 = () -> {
            System.out.println("线程t2开始");
            Object value=cacheTest.getCache("department","T2");
            System.out.println("线程t2获取到的缓存值："+value);
            System.out.println("线程t2结束");
        };
        new Thread(task2).start();
 
        Runnable task3 = () -> {
            System.out.println("线程t3开始");
            Object value=cacheTest.getCache("department","T3");
            System.out.println("线程t3获取到的缓存值："+value);
            System.out.println("线程t3结束");
        };
        new Thread(task3).start();
 
 
        // 下面是java7，等同于上面代码
        /*Thread t1=new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("线程t1开始");
                Object value=cacheTest.getCache("key1","T1");
                System.out.println("线程t1获取到的缓存值："+value);
                System.out.println("线程t1开始");
            }
        });
        Thread t2=new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("线程t2开始");
                Object value=cacheTest.getCache("key1","T2");
                System.out.println("线程t2获取到的缓存值："+value);
                System.out.println("线程t2结束");
            }
        });
        Thread t3=new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("线程t3开始");
                Object value=cacheTest.getCache("key3","T3");
                System.out.println("线程t3获取到的缓存值："+value);
                System.out.println("线程t3结束");
            }
        });
        t1.start();
        t2.start();
        t3.start();*/
    }
 
    /**
     * 员工假数据（实际来源可能是数据库或远程接口）
     * @return 人员列表
     */
    private static List<Employee> getEmployeeCacheList(){
        List<Employee> employeeList = new ArrayList<>();
        Employee employee1 = new Employee("Jalen","102","20171121");
        Employee employee2 = new Employee("Jermy","101","20171122");
        Employee employee3 = new Employee("Abely","104","20171123");
        Employee employee4 = new Employee("Mercy","103","20171124");
        employeeList.add(employee1);
        employeeList.add(employee2);
        employeeList.add(employee3);
        employeeList.add(employee4);
        return employeeList;
    }
 
    /**
     * 部门假数据（实际来源可能是数据库或远程接口）
     * @return 部门列表
     */
    private static List<Department> getDepartmentCacheList(){
        List<Department> departmentList = new ArrayList<>();
        Department department1 = new Department("101","采购部","1");
        Department department2 = new Department("102","财务部","1");
        Department department3 = new Department("103","信管部","1");
        Department department4 = new Department("104","外贸部","1");
        departmentList.add(department1);
        departmentList.add(department2);
        departmentList.add(department3);
        departmentList.add(department4);
        return departmentList;
    }
 
    /**
     * 公司假数据（实际来源可能是数据库或远程接口）
     * @return 公司列表
     */
    private static List<Company> getCompanyCacheList(){
        List<Company> companyList = new ArrayList<>();
        Company company1 = new Company("1","Sanmina");
        Company company2 = new Company("2","Google");
        Company company3 = new Company("3","Tecent");
        companyList.add(company1);
        companyList.add(company2);
        companyList.add(company3);
        return companyList;
    }
}
```

# 公司实体类
```java
package com.example.google.guava.demo.model;
 
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
 
/**
 * <p>
 * <code>Company</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/20 10:14
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Company {
 
    private String companyId;
 
    private String companyName;
}
```

# 部门实体类
```java
package com.example.google.guava.demo.model;
 
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
 
/**
 * <p>
 * <code>Department</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/20 10:10
 */
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Department {
 
    private String departmentId;
 
    private String departmentName;
 
    private String companyId;
}
```

# 员工实体类
```java
package com.example.google.guava.demo.model;
 
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
 
/**
 * <p>
 * <code>Employee</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/20 9:19
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Employee {
 
    String name;
 
    String departmentId;
 
    String employeeId;
}
```

# 输出结果
如上，当线程2运行过后，部门数据已经缓存到cache对象当中，当线程3再去获取时，就可以直接从缓存中获取数据了

```
线程t2开始
线程t3开始
线程：T2获取数据
线程t1开始
线程：T3获取数据
线程：T1获取数据
###获取部门缓存数据###
线程t2获取到的缓存值：[Department(departmentId=101, departmentName=采购部, companyId=1), Department(departmentId=102, departmentName=财务部, companyId=1), Department(departmentId=103, departmentName=信管部, companyId=1), Department(departmentId=104, departmentName=外贸部, companyId=1)]
线程t2结束
###获取公司缓存数据###
线程t3获取到的缓存值：[Department(departmentId=101, departmentName=采购部, companyId=1), Department(departmentId=102, departmentName=财务部, companyId=1), Department(departmentId=103, departmentName=信管部, companyId=1), Department(departmentId=104, departmentName=外贸部, companyId=1)]
线程t3结束
线程t1获取到的缓存值：[Company(companyId=1, companyName=Sanmina), Company(companyId=2, companyName=Google), Company(companyId=3, companyName=Tecent)]
线程t1开始
```

# 参考
http://blog.csdn.net/michaelwubo/article/details/50865185
