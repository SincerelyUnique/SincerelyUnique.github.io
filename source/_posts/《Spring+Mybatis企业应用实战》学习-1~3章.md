---
title: 《Spring+Mybatis企业应用实战》学习-1~3章
date: 2017-08-07 12:03:46
tags:
- spring
- mybatis
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. Mybatis是相对于Hibernate这种一站式的ORM来讲，它是“半自动化”的；


2. Model1和Model2

① Model1模式：Web应用几乎全部由JSP页面（身兼View和Controller角色）组成，用少量的JavaBean来处理数据库连接等操作；

② Model2模式：Servlet为前端控制器，后端JavaBean完成实际逻辑处理，最后使用JSP处理显示逻辑；Model2是MVC设计思想；


3.MVC与观察者模式对比

① 观察者模式：观察者与被观察者可以使两个互相对等的对象；

② MVC：被观察者往往只是单纯的数据体，而观察者则是单纯的试图页面；


4. Spring的核心容器必须依赖common-logging Jar包


5. DispatcherServlet（核心功能是分发请求，分发给对应处理的java类，成Handle）加载时需要一个Spring MVC配置文件，默认加载/WEB-INF/springmvc-servlet.xml


6. Spring2.5之后新增基于注解的控制器@Controller，不用实现Controller接口，2.5以后推荐使用注解；


7. 配置DispatcherServlet；
```xml
	<!-- spring mvc -->
	<servlet>
		<description>spring mvc servlet</description>
		<servlet-name>springMvc</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<description>spring mvc 配置文件</description>
			<param-name>contextConfigLocation</param-name>
			<param-value>
                classpath*:spring/spring-mvc.xml
            </param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<!--让Spring MVC的前端控制器拦截所有请求-->
	<servlet-mapping>
		<servlet-name>springMvc</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
```

8. Spring MVC建议把所有的视图页面存放在WEB-INF文件夹下面，这样可以保护视图页面，避免直接向视图页面发送请求，Spring MVC视图解析器配置：

```xml
	<!-- 对模型视图名称的解析，即在模型视图名称添加前后缀 -->
	<bean 
		class="org.springframework.web.servlet.view.InternalResourceViewResolver"
		p:prefix="/WEB-INF/views/" p:suffix=".jsp" />
```

9. Spring可以配置包扫描，扫描包下面的java文件，如果扫描到有Spring的相关注解的类，则把这些类注册为Spring的bean：

```xml
	<!-- 配置要扫描的包 -->
	<!-- 使用Annotation自动注册Bean，解决事物失效问题：在主容器中不扫描@Controller注解，在SpringMvc中只扫描@Controller注解。  -->
	<context:component-scan base-package="com.demo"><!-- base-package 如果多个，用“,”分隔 -->
		<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
	</context:component-scan>
```

10. Spring MVC执行流程：

① DispatcherServlet截获用户请求；

② DispatcherServlet对请求的URL解析，得到URI，调用HandlerMapping获得该Handle配置的所有相关对象，包括Handler对象及其对应的拦截器，这些对象封装到一个HandlerExecutionChain对象中返回；

③ DispatcherServlet根据获得的Handler选择一个合适的HandlerAdapter；

④ 根据请求中的数据模型，开始执行Handler（Controller），这个过程Spring会帮助做一些额外的工作：消息转换、数据类型转换、数据格式化、数据验证；

⑤ Handler执行完成后会向DispatcherServlet返回一个ModelAndView对象（包含视图名或视图名和模型）；

⑥ 根据返回的ModelAndView对象，选择一个合适的ViewResolver视图解析器返回给DispatcherServlet；

⑦ ViewResolver结合Model和View来渲染视图，并将视图渲染结果返回给客户端；


11. Spring MVC常用注解：

① 启用：spring配置文件在头文件加入spring-context，启用并配置包扫描<context:component-scan/>元素（以便注册bean）；

② RequestMapping：用于映射一个请求，注解属性有value、name、method、consumes（指定contentType）、produces（指定返回内容类型）、params、headers、Path；

③ 参数绑定注解：根据处理的request内容的不同可以分为四类：
- （1）处理request body部分的注解：@RequestParam、@RequestBody；
- （2）处理request uri部分的注解：@PathVariable；
- （3）处理request header部分的注解：@RequestHeader、@CookieValue；
- （4）处理attribute类型的注解：@SessionAttributes、@ModelAttribute；

④ @RequestParam：属性有name、value、required、defaultValue；

⑤ @PathVariable：
```
  @RequestMapping(value="/home/users/{userId}")
  public String getUser(@PathVariable Integer userId){ ... }
```

⑥ @RequestHeader：用于将请求的头信息区的数据映射到功能处理方法的参数上，属性有name、value、required、defaultValue；

⑦ @CookieValue：将请求的cookie数据映射到参数上，属性有name、value、required、defaultValue；

⑧ @SessionAttributes：允许我们有选择地指定Model中的哪些数据需要转存到HttpSession对象当中，只能声明到类上，不能定义在方法上；

```
//对应方法中需要有一个model.addAttribute("user",user);这里“user”是一个对象
@SessionAttributes("user")
//可以设置多个对象到HttpSession中
//@SessionAttributes(types = {User.class, Company.class},value = {"user","company"})
```

⑨ @ModelAttribute：在一个控制器类中先于其他映射的方法执行，并根据其具体的value值绑定方法的参数亦或是返回一个Model对象；

12. Spring3.0新增HttpMessageConverter<T>接口，负责将请求信息转换为一个对象，并将对象绑定到请求方法的参数中或输出为相应信息；


13. @RequestBody不能处理multipart/form-data这种格式数据；application/json、application/xml必须使用@RequestBody来处理；


14. Spring默认使用Jackson处理json数据，如果使用其他开源包来处理json（如fastjson），则需要在配置文件中配置HttpMessageConverter，在<mvc:message-converters/>标签中配置你使用的开源包中实现HttpMessageConverter接口的转换器的bean；当然，也可以自定义HttpMessageConverter；


