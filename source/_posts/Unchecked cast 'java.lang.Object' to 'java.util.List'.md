---
title: Unchecked cast 'java.lang.Object' to 'java.util.List'
date: 2017-10-11 13:41:09
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 出现原因，实际应用时map集合中存在多个键值对，而由于每个键对应的值为不同类型，当存在一个值的类型为List时，在接收时做强制转换会出现此警告

2. 出错代码举例

```java
  Map<String,Object> repeatMap = checkAndRemoveRepeatRecords(dataList);

  int dataRepeatCounter = (int)repeatMap.get("dataRepeatCounter");
  List<String[]> dataList = (List<String[]>)repeatMap.get("dataList");
```

3. 修改方法1（加unchecked注解）

```java
public class Test {
    public static void main(String[] args) {
        test1();
    }
 
    @SuppressWarnings("unchecked")
    private static void test1() {
        Map<String, Object> map_original_foo = new HashMap<>();
        Map<String, Integer> map_original_son = new HashMap<>();
        map_original_son.put("a", 1);
        map_original_son.put("b", 2);
        map_original_son.put("c", 3);
 
        map_original_foo.put("aa", map_original_son);
 
        Map<String, Integer> map_current_son = (Map<String, Integer>) map_original_foo.get("aa");
        System.out.println(map_current_son);
    }
}
```

4. 修改方法2（类型检查）

```java
public class Test {
    public static void main(String[] args) {
        testMap();
        testList();
    }
 
    private static void testMap() {
        Map<String, Object> map_original_foo = new HashMap<>();
        Map<String, Integer> map_original_son = new HashMap<>();
        map_original_son.put("a", 1);
        map_original_son.put("b", 2);
        map_original_son.put("c", 3);
 
        map_original_foo.put("aa", map_original_son);
 
        if (map_original_foo.get("aa") instanceof Map<?, ?>) {
            Map<?, ?> map_current_foo = (Map<?, ?>) map_original_foo.get("aa");
            Class keyClass = map_current_foo.entrySet().stream().findFirst().map(entry -> entry.getKey().getClass()).orElse(null);
            Class valueClass = map_current_foo.entrySet().stream().findFirst().map(entry -> entry.getValue().getClass()).orElse(null);
 
            if (String.class.equals(keyClass) && Integer.class.equals(valueClass)) {
                Map<String, Integer> map_current_son = new HashMap<>();
                for (Map.Entry entry : map_current_foo.entrySet()) {
                    String key = (String) entry.getKey();
                    Integer value = (Integer) entry.getValue();
                    map_current_son.put(key, value);
                }
                System.out.println(map_current_son);
            }
        }
    }
 
    private static void testList() {
        Map<String, Object> map_original_foo = new HashMap<>();
        List<String> list_original_son = new ArrayList<>();
        list_original_son.add("aa");
        list_original_son.add("bb");
        list_original_son.add("cc");
        map_original_foo.put("dd", list_original_son);
 
        if (map_original_foo.get("dd") instanceof List<?>) {
            List<?> temp_list = (List<?>) map_original_foo.get("dd");
            Class valueClass = temp_list.stream().findFirst().map(Object::getClass).orElse(null);
            if (String.class.equals(valueClass)) {
                List<String> list_current_son = new ArrayList<>();
                for (Object s: temp_list) {
                    String value = (String) s;
                    list_current_son.add(value);
                }
                System.out.println(list_current_son);
            }
        }
    }
}
```

5. 参考：

http://www.xinotes.net/notes/note/1412/

https://stackoverflow.com/questions/12566757/intellij-warning-generic-unchecked-assignment

http://blog.csdn.net/qq_27093465/article/details/51910411
