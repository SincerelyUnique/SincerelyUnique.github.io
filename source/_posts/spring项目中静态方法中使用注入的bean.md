---
title: spring项目中静态方法中使用注入的bean
date: 2017-12-28 12:15:38
tags:
- spring
- bean
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 说明

项目中涉及到dubbo服务，在项目中需要将这个服务作为一个bean注入，由于本人需要在静态方法中使用这个bean，所以如果使用类似@Autowire等注解注入时会编译报错。

# 解决方法

自己写一个工具类，通过spring上下文获取这个bean。转成静态的。

```java
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;
 
/**
 * <p>
 * <code>SpringContextHelper</code>
 * </p>
 * Description:
 *
 * @author xxx
 * @date 2017/12/28 10:34
 */
 
/*
 * @Component泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。
 * 标记为组件后在启动服务时，会将此类实例化到spring容器中，相当于配置文件中的<bean id="" class=""/>
 */
@Component
public class SpringContextHelper implements ApplicationContextAware{
 
    /**
     * Spring应用上下文环境
     */
    private static ApplicationContext applicationContext;
 
    /**
     * 重写并初始化上下文
     * @param applicationContext 应用上下文
     * @throws BeansException bean异常
     */
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        // 初始化applicationContext
        SpringContextHelper.applicationContext = applicationContext;
    }
 
    /**
     * 通过类获取
     * @param clazz 注入的类
     * @param <T> 返回类型
     * @return 返回这个bean
     * @throws BeansException bean异常
     */
    @SuppressWarnings("unchecked")
    public static <T> T getBean(Class clazz) throws BeansException {
        return  (T)applicationContext.getBean(clazz);
    }
 
    /**
     * 通过名字获取
     * @param name 名字
     * @param <T> 返回类型
     * @return 返回这个bean
     * @throws BeansException bean异常
     */
    @SuppressWarnings("unchecked")
    public static <T> T getBean(String name) throws BeansException {
        return (T) applicationContext.getBean(name);
    }
}
```

在需要使用的类中注入你的bean使用即可

```java
private static EmployeeService employeeService = SpringContextHelper.getBean(EmployeeService.class);
```

网上还有类似的解决办法，例如使用@PostConstruct等，或是直接先实例化这个类，再调用，如new TestClass().employeeService（在这里肯定不适用），我都尝试了下，没有成功。
