---
title: 使用Spring PropertiesLoaderUtils读取properties属性文件
date: 2017-08-09 09:56:56
tags:
- spring
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

场景：读取properties属性文件。

1. 定义属性文件 （在classpath下，我直接放在resources路径下）：server.properties

```
##server info
server.ip=192.168.59.58
server.port=8080
```

2. 读取

```java
    private static String[] getServerInfo(String label){
        String[] arr = new String[2];
        try {
            Properties properties = PropertiesLoaderUtils.loadAllProperties("server.properties");
            arr[0] = properties.getProperty("server.ip");
            arr[1] = properties.getProperty("server.port");
        }catch (IOException ioe){
            ioe.printStackTrace();
        }
        return arr;
    }
```

3. 缓存属性文件

```java
public class PropertyUtils {
 
    private static final String SYSTEM_PATH = "config/system.properties";
 
    private static final String GENERATOR_PATH = "config/generator.properties";
 
    /**
     * 缓存配置文件里的值集合
     */
    private static HashMap<String, HashMap<String, String>> valueMap = new HashMap<String, HashMap<String, String>>();
 
    /**
     * 缓存配置文件的值
     * @param propName 配置文件名称
     */
    private static void cacheProperties(String propName) {
        try {
            Properties properties = PropertiesLoaderUtils.loadAllProperties(propName);
            if (properties != null) {
                HashMap<String, String> map = new HashMap<String, String>();
                Enumeration<Object> keys = properties.keys();
                while (keys.hasMoreElements()) {
                    String key = (String) keys.nextElement();
                    map.put(key, properties.getProperty(key));
                }
                valueMap.put(propName, map);
            }
        }catch (IOException e){
            e.printStackTrace();
        }
    }
 
    /**
     * 从缓存中获取值 如果没有则重新读取文件
     */
    public static String getValue(String key, String propName) {
        if (!valueMap.containsKey(propName)) {
            cacheProperties(propName);
        }
 
        HashMap<String, String> map = valueMap.get(propName);
        if (map.containsKey(key)) {
            return map.get(key);
        }
        return null;
    }
 
    /**
     * 测试
     * @param args args
     */
    public static void main(String[] args) {
        String str = getValue("server.ip",SYSTEM_PATH);
        System.out.println(str);
    }
}
```



