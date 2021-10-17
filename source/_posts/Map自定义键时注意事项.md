---
title: Map自定义键时注意事项
date: 2017-10-12 17:17:18
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```java
public static void main(String[] args) {
 
        /*
         * 1.Map的键是String,Integer等类的话，就会重写HashCode()和equals()方法，
         *   键的值一样，后面的就会覆盖前面的
         */
        Map<Integer,String> map1 = new HashMap<>();
        map1.put(0,"a");
        map1.put(1,"b");
        map1.put(2,"c");
        map1.put(2,"d");
        System.out.println(map1.toString());
        System.out.println("#####################");
 
 
        /*
         * 2.键是自定义类的话，需要在自定义类中重写HashCode()和equals()方法，
         *   否则就算键的值一样，也会加进去的。
         */
        // 2.1未定义了hashcode和equals的属性作为键
        Map<User,String> map2 = new HashMap<>();
        map2.put(new User("jalen","123456"),"a");
        map2.put(new User("abely","123456"),"b");
        map2.put(new User("mercy","123456"),"c");
        map2.put(new User("jermy","123456"),"d");
        map2.put(new User("jermy","123456"),"e");
        for(User user : map2.keySet())
        {
            System.out.println(user.toString()+":"+map2.get(user));
        }
        System.out.println("#####################");
 
 
        // 2.1 定义了hashcode和equals的属性作为键
        Map<Manager,String> map3 = new HashMap<>();
        map3.put(new Manager("jalen","123456"),"aa");
        map3.put(new Manager("abely","123456"),"bb");
        map3.put(new Manager("mercy","123456"),"cc");
        map3.put(new Manager("jermy","123456"),"dd");
        map3.put(new Manager("jermy","123456"),"ee");
        for (Manager manager: map3.keySet()) {
            System.out.println(manager.toString()+":"+map3.get(manager));
        }
 
    }
```

运行结果：

```
{0=a, 1=b, 2=d}
#####################
User(username=jermy, password=123456):d
User(username=mercy, password=123456):c
User(username=jermy, password=123456):e
User(username=jalen, password=123456):a
User(username=abely, password=123456):b
#####################
Manager(managerName=jalen, managerPassword=123456):aa
Manager(managerName=abely, managerPassword=123456):bb
Manager(managerName=mercy, managerPassword=123456):cc
Manager(managerName=jermy, managerPassword=123456):ee
```

附录：User实体和Manager实体

```java
import lombok.AllArgsConstructor;
import lombok.ToString;
 
@AllArgsConstructor
@ToString
public class User {
 
    private String username;
 
    private String password;
 
}
```

```java
import lombok.Data;
import lombok.NoArgsConstructor;
 
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Manager {
 
    private String managerName;
 
    private String managerPassword;
}
```
