---
title: spring定时器的基本使用
date: 2017-09-18 15:24:53
tags:
- spring
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 使用注解：定义一个组件，扫描一下包即可
```java
package com.spring.schedule.demo01;
 
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.EnableScheduling;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
 
import java.text.SimpleDateFormat;
import java.util.Date;
 
@Component
@EnableScheduling
public class ScheduledTasksForAnnotation {
 
    private static final Logger log = LoggerFactory.getLogger(ScheduledTasksForAnnotation.class);
 
    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");
 
    //每隔5s执行一次
    @Scheduled(fixedRate = 5000)
    //每天12点 24点执行
    //@Scheduled(cron = "0 0 12,0 * * ?")
    public void reportCurrentTime() {
        System.out.println("日志打印时间："+dateFormat.format(new Date()));
    }
 
}
```
```xml
    <!-- 配置要扫描的包 -->
    <!-- 使用Annotation自动注册Bean，解决事物失效问题：在主容器中不扫描@Controller注解，在SpringMvc中只扫描@Controller注解。  -->
    <context:component-scan base-package="com.spring.schedule"><!-- base-package 如果多个，用“,”分隔 -->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
```
如果不想使用@EnableScheduling, 则需增加如下配置
```xml
    <!--方法一：task任务扫描注解,扫描定时任务,spring3.1后可以使用@EnableScheduling注解替代，如果不想使用注解，把下面打开即可-->
    <!--<task:annotation-driven />-->
    <!--<context:component-scan base-package="com.spring.schedule.demo01" />-->
```

# 使用quartz
增加一个spring-jobs.xml配置，引入到spring配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xmlns:cache="http://www.springframework.org/schema/cache"
	xsi:schemaLocation="
http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context-3.0.xsd 
http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache.xsd" >
 
    <!-- ======================== 创建调度工厂 ======================== -->
    <bean id="SpringJobSchedulerFactoryBean" class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
        <property name="triggers">
            <list>
                <!-- 计划开始定时任务调度,此处可以添加多个定时任务 -->
                <ref local="myTaskTrigger"/>
                <!--<ref local="..."/>-->
            </list>
        </property>
    </bean>
 
    <!-- ======================== 创建调度工厂中的一个调度触发器 ======================== -->
    <!-- 定时定时任务的触发器 -->
    <bean id="myTaskTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"><!--CronTriggerBean-->
        <property name="jobDetail">
            <ref bean="myTask" />
        </property>
        <property name="cronExpression">
            <!-- 每天凌晨3：30分执行任务  -->
            <!--<value>0 30 3 * * ?</value>-->
 
            <!-- 每5s执行一次任务  -->
            <value>*/5 * * * * ?</value>
        </property>
    </bean>
 
    <!-- ======================== 这个触发器触发的实际任务 ======================== -->
    <bean id="myTask"
          class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
        <property name="targetObject">
            <bean class="com.spring.schedule.demo02.ScheduledTaskForQuartz" />
        </property>
        <property name="targetMethod">
            <value>reportCurrentTime</value>
        </property>
    </bean>
 
</beans>
```
下面是实际任务的类
```java
package com.spring.schedule.demo02;
 
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
 
import java.text.SimpleDateFormat;
import java.util.Date;
 
@Component
public class ScheduledTaskForQuartz {
 
    private static final Logger log = LoggerFactory.getLogger(ScheduledTaskForQuartz.class);
 
    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");
 
    public void reportCurrentTime() {
        System.out.println("日志打印时间："+dateFormat.format(new Date()));
    }
 
}
```
代码可以参考我的github：git@github.com:SincerelyUnique/spring-task.git
