---
title: spring data redis简单连接测试redis
date: 2020-06-17 21:45:19
tags:
- spring data
- redis
categories:
- [学习, 数据库学习, Redis]
---

## 使用idea创建spring boot项目

> idea->File->New->Project->Spring Initializr->Next->Next->NoSQL->Spring Data Redis->Next->Finish

## 修改配置application.properties

```
spring.redis.host=127.0.0.1
spring.redis.port=6379
spring.redis.password=1qaz@wsx
```

## 修改启动类DemoApplication.java

```java
package com.example.demo;
 
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StringRedisTemplate;
 
@SpringBootApplication
public class DemoApplication {
 
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
 
    @Autowired
    public DemoApplication(RedisTemplate redisTemplate,
                           StringRedisTemplate stringRedisTemplate) {
        this.redisTemplate = redisTemplate;
        this.stringRedisTemplate = stringRedisTemplate;
    }
    private RedisTemplate redisTemplate;
    private StringRedisTemplate stringRedisTemplate;
 
    @Bean
    @SuppressWarnings("unchecked")
    CommandLineRunner runRedisTemplateTest(){
	    return args -> {
            System.out.println("redisTemplate");
            redisTemplate.opsForValue().set("name", "Kitty");
            Object name = redisTemplate.opsForValue().get("name");
            System.out.println(name);
            System.out.println("-----------End---------");
        };
    }
 
    @Bean
    CommandLineRunner runStringRedisTemplateTest(){
        return args -> {
            System.out.println("stringRedisTemplate start");
            Object name = stringRedisTemplate.opsForValue().get("name");
            System.out.println(name);
            System.out.println("-----------End---------");
        };
    }
}
```

## 右键运行启动类DemoApplication.java, 输出如下

```
redisTemplate
Kitty
-----------End---------
stringRedisTemplate start
Jalen
-----------End---------
```

遇到的问题：

当我们使用redis-cli执行set name Jalen后，通过redisTemplate获取name总是获取不到，但是通过stringRedisTemplate可以获取到，原因是两者的默认使用的序列化方式不一致

```
// redisTemplate默认使用JdkSerializationRedisSerializer
// stringRedisTemplate默认使用StringRedisSerializer
// 如果想让redisTemplate输出正常，可以手动设置一下redisTemplate的key/value序列化方式，即
redisTemplate.setKeySerializer(new StringRedisSerializer());
redisTemplate.setValueSerializer(new StringRedisSerializer());
 
// 其实这种情况有些多余，直接使用stringRedisTemplate就好啦
```

使用python连接redis

```python
import redis

pool = redis.ConnectionPool(host="127.0.0.1", port=6379, password="1qaz@wsx", max_connections=1024)
conn = redis.Redis(connection_pool=pool)
print(conn.get("name"))
```
