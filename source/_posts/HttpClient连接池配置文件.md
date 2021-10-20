---
title: HttpClient连接池配置文件
date: 2017-08-07 08:39:29
tags:
- httpclient
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

配置http连接池：

# 在spring-context.xml配置文件中引入

```xml
<import resource="classpath:spring/spring-httpclient.xml" />
```

# 连接池配置文件spring-httpclient.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
 
    <!--配置一个httpClient 连接池 -->
    <bean id="poolHttpClientConnectionManager"
          class="org.apache.http.impl.conn.PoolingHttpClientConnectionManager">
        <!-- 最大链接数 -->
        <property name="maxTotal" value="200"/>
        <!-- 设置每个主机地址的并发数 -->
        <property name="defaultMaxPerRoute" value="20"/>
    </bean>
 
    <!-- 生成httpClientBuilder(用于得到httpClient) -->
    <bean id="httpClientBuilder" class="org.apache.http.impl.client.HttpClientBuilder">
        <!-- 注入连接池 -->
        <property name="connectionManager" ref="poolingHttpClientConnectionManager"/>
    </bean>
 
    <!-- 配置httpClient (通过httpClientBuilder得到httpClient对象，并且要设置httpClient为多利模式) -->
    <bean class="org.apache.http.impl.client.CloseableHttpClient"
          factory-bean="httpClientBuilder" factory-method="build" scope="prototype"></bean>
 
    <!-- 构造(配置)请求参数 -->
    <bean id="requestConfigBuilder" class="org.apache.http.client.config.RequestConfig.Builder">
        <!-- 创建连接的最长时间 -->
        <property name="connectTimeout" value="1000"/>
        <!-- 从连接池中获取到连接的最长时间 -->
        <property name="socketTimeout" value="10000"/>
        <!-- 提交请求前测试连接是否可用 -->
        <property name="connectionRequestTimeout" value="500"/>
    </bean>
 
    <bean class="org.apache.http.client.config.RequestConfig"
          factory-bean="requestConfigBuilder" factory-method="build"></bean>
 
    <!-- 定期清理无效的连接 -->
    <bean class="com.a.b.common.utils.http.IdleConnectionEvictor" destroy-method="shutdown">
        <constructor-arg index="0" ref="poolingHttpClientConnectionManager" />
        <!-- 间隔一分钟清理一次 -->
        <constructor-arg index="1" value="60000" />
    </bean>
 
</beans>
```

# 类IdleConnectionEvictor

```java
public class IdleConnectionEvictor extends Thread{
 
    private final HttpClientConnectionManager connMgr;
 
    private Integer waitTime;
 
    private volatile boolean shutdown;
 
    private static final Logger logger = Logger.getLogger(HttpPoolUtil.class);
 
    public IdleConnectionEvictor(HttpClientConnectionManager connMgr, Integer waitTime) {
        this.connMgr = connMgr;
        this.waitTime = waitTime;
        this.start();
    }
 
    @Override
    public void run() {
        try {
            while (!shutdown) {
                synchronized (this) {
                    wait(waitTime);
                    // 关闭失效的连接
                    connMgr.closeExpiredConnections();
                }
            }
        } catch (InterruptedException ex) {
            logger.error("清理异常");
        }
    }
 
    /**
     * 销毁释放资源
     */
    public void shutdown() {
        shutdown = true;
        synchronized (this) {
            notifyAll();
        }
    }
}
```


