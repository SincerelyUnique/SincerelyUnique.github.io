---
title: Map遍历几种方式
date: 2017-10-13 08:57:41
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```java
    public static void main(String[] args) {
        Map<Integer, String> map = new HashMap<>();
        map.put(1, "a");
        map.put(2, "b");
        map.put(3, "ab");
        map.put(4, "ab");
 
        System.out.println("##JAVA7##");
        System.out.println("--------------1-----------------");
 
        // 1.通过Foreach遍历Map.keySet
        for (Integer key:map.keySet()) {
            String value = map.get(key);
            System.out.println("key="+ key +";value="+ value);
        }
 
        System.out.println("--------------2-----------------");
 
        // 2.通过Foreach遍历Map.values()，不能遍历键
        for (String value: map.values()) {
            System.out.println("value="+value);
        }
 
        System.out.println("--------------3-----------------");
 
        // 3.使用Foreach遍历Map.entrySet
        for (Map.Entry<Integer,String> entry: map.entrySet()) {
            Integer key = entry.getKey();
            String value = entry.getValue();
            System.out.println("key="+ key +";value="+ value);
        }
 
        System.out.println("--------------4-----------------");
 
        // 4.使用Iterator遍历Map.entrySet
        Iterator<Map.Entry<Integer, String>> it = map.entrySet().iterator();
        while (it.hasNext()){
            Map.Entry<Integer, String> entry = it.next();
            Integer key = entry.getKey();
            String value = entry.getValue();
            System.out.println("key="+ key +";value="+ value);
        }
 
        System.out.println("--------------5-----------------");
 
        // 5.同4，但不使用泛型
        Iterator it1 = map.entrySet().iterator();
        while (it1.hasNext()){
            Map.Entry entry = (Map.Entry) it1.next();
            Integer key = (Integer)entry.getKey();
            String value = (String)entry.getValue();
            System.out.println("Key = " + key + ", Value = " + value);
        }
 
 
        System.out.println("##JAVA8##");
        System.out.println("--------------1-----------------");
 
        // 1.通过Foreach遍历Map.keySet
        map.keySet().forEach(key-> System.out.println("key="+key+";value="+map.get(key)));
 
        System.out.println("--------------2-----------------");
 
        // 2.通过Foreach遍历Map.values()，不能遍历键
        map.values().forEach(System.out::println);
 
        System.out.println("--------------3-----------------");
 
        // 3.使用Foreach遍历Map.entrySet
        map.entrySet().forEach(entry-> System.out.println("key="+entry.getKey()+";value="+entry.getValue()));
 
        System.out.println("--------------4-----------------");
 
        // 4.使用Iterator遍历Map.entrySet
        map.entrySet().iterator().forEachRemaining(item-> System.out.println("key="+item.getKey()+";value="+item.getValue()));
 
        System.out.println("--------------5-----------------");
 
        // 5.java8新增
        map.forEach( (k,v)-> System.out.println("key="+k+";value="+v) );
    }
```

附录：

性能说明参考：http://www.jb51.net/article/59874.htm
