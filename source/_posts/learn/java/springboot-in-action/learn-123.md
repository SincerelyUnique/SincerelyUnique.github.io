---
title: 阅读笔记(1,2,3章节)
date: 2021-05-13 22:38:43
categories:
- [学习, Java语言学习, SpringBoot实战]
tags:
- java
- springboot
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```bash
《Spring Boot in action》阅读笔记(1,2,3章节)
 
 
中文名：Spring Boot实战
英文名：Spring Boot in action
作者：Craig Walls
译者：丁雪丰
出版社：人民邮电出版社2016年9月第1版
随书源码：www.manning.com/books/spring-boot-in-action
注：随书源码版本比较老，可以按自己的来
 
 
2004年，Spring Framework 1.0正式发布
Spring Boot： 部署、监控、度量等
Spring Cloud：服务发现、服务降级（熔断）、监控等
 
 
演进：
Spring 1.0：Spring 1.0的出现彻底改变了我们开发企业级Java应用程序的方式。 Spring的依赖注入
与声明式事务意味着组件之间再也不存在紧耦合，再也不用重量级的EJB了。
Spring 2.0：到了Spring 2.0，我们可以在配置里使用自定义的XML命名空间，更小、更简单易懂的配
置文件让Spring本身更便于使用。
Spring 2.5：Spring 2.5让我们有了更优雅的面向注解的依赖注入模型（即@Component和@Autowired
注解），以及面向注解的Spring MVC编程模型。不用再去显式地声明应用程序组件了，也
不再需要去继承某个基础的控制器类了。
Spring 3.0：到了Spring 3.0，我们有了一套基于Java的全新配置，它能够取代XML。在Spring 3.1里，
一系列以@Enable开头的注解进一步完善了这一特性。终于，我们第一次可以写出一个
没有任何XML配置的Spring应用程序了。
Spring 4.0：Spring 4.0对条件化配置提供了支持，根据应用程序的Classpath、环境和其他因素，运
行时决策将决定使用哪些配置，忽略哪些配置。那些决策不需要在构建时通过编写脚本确定了；以前会
把选好的配置放在部署的包里，现在情况不同了。
Spring 5.0：即Spring Boot，实现了自动配置，起步依赖，命令行界面（如Spring Boot CLI结合Groovy），
Actuator等。
 
 
假设开发一个简单的Hello World Web应用程序，正常我们该做些什么？
1.一个项目结构，其中有一个包含必要依赖的Maven或者Gradle构建文件，最起码要有Spring
  MVC和Servlet API这些依赖。
2.一个web.xml文件（或者一个WebApplicationInitializer实现），其中声明了Spring
  的DispatcherServlet。
3.一个启用了Spring MVC的Spring配置。
4.一个控制器类，以“Hello World”响应HTTP请求。
5.一个用于部署应用程序的Web应用服务器，比如Tomcat。
事实上，这份清单里只有一个东西是和Hello World功能相关的，即控制器，剩
下的都是Spring开发的Web应用程序必需的通用样板。
而现在我们可以很简单地启动一个spring web程序，参照随书源码ch01，先下载一下Spring Boot CLI，
下好可以将bin路径配置到系统path，然后命令行执行spring run HelloController.groovy
 
 
Spring Boot精要，下述1，2，3旨在简化Spring开发，4则提供在运行时检视应用程序内部情况的能力。
1.自动配置：针对很多Spring应用程序常见的应用功能， Spring Boot能自动提供相关配置。
  比如如果你写过用JDBC访问关系型数据库的应用程序，那你一定在Spring应用程序上下文里配置过
  JdbcTemplate这个Bean并注入一个DataSource依赖，这其实是一个样板配置。
  而Spring Boot如果在Classpath里发现JdbcTemplate，那么它会为你自动配置一个JdbcTemplate的Bean。
2.起步依赖：告诉Spring Boot需要什么功能，它就能引入需要的库。起步依赖其实就是特殊的Maven依
  赖和Gradle依赖，利用了传递依赖解析，把常用库聚合在一起，组成了几个为特定功能而定制的依赖。
  比如你正在用Spring MVC构造一个REST API，并将JSON作为资源描述，此外，你还想运用遵循JSR-303
  规范的声明式校验，并使用嵌入式的Tomcat服务器来提供服务，那么你至少需要以下8个依赖：
   org.springframework:spring-core
   org.springframework:spring-web
   org.springframework:spring-webmvc
   com.fasterxml.jackson.core:jackson-databind
   org.hibernate:hibernate-validator
   org.apache.tomcat.embed:tomcat-embed-core
   org.apache.tomcat.embed:tomcat-embed-el
   org.apache.tomcat.embed:tomcat-embed-logging-juli
  如果利用Spring Boot的起步依赖，只需配置一个，它会根据依赖传递把其他所需依赖引入项目里。
   org.springframework.boot:spring-boot-starter-web
  而且Spring Boot的每一种起步依赖都实际上制定了应用程序所需的一类功能，如jpa、security等
3.命令行界面：这是Spring Boot的可选特性（非必要组成部分），借此你只需写代码就能完成完整
  的应用程序，无需传统项目构建。
  Spring Boot CLI利用了起步依赖和自动配置，让只写代码即可实现应用程序成为可能。
  比如随书源码ch01里的groovy代码，除了起步依赖和自动配置，而且代码中没有import任何包。
  命令行界面不太好的地方是引入了一套不太常规的开发模型。
4.Actuator：让你能够深入运行中的Spring Boot应用程序，一探究竟。如
  Spring应用程序上下文里配置的Bean；
  Spring Boot的自动配置做的决策
  应用程序取到的环境变量、系统属性、配置属性和命令行参数
  应用程序里线程的当前状态
  应用程序最近处理过的HTTP请求的追踪情况
  各种和内存用量、垃圾回收、 Web请求以及数据源用量相关的指标
 
 
注意：Spring Boot不是应用服务器
误解的产生：Spring Boot可以把Web应用程序变为可自执行的JAR文件，不用部署到传统Java应用服务
      器里就能在命令行里运行。
理由：Spring Boot在应用程序里嵌入了一个Servlet容器（Tomcat、 Jetty或Undertow），以此实现
      这一功能。但这是内嵌的Servlet容器提供的功能，不是Spring Boot实现的。
与上面类似，Spring Boot也没有实现诸如JPA或JMS（Java Message Service， Java消息服务）之
类的企业级Java规范。它的确支持不少企业级Java规范，但是要在Spring里自动配置支持那些特
性的Bean。例如，Spring Boot没有实现JPA，不过它自动配置了某个JPA实现（比如Hibernate）的
Bean，以此支持JPA。
最后，Spring Boot没有引入任何形式的代码生成，而是利用了Spring 4的条件化配置特性，
以及Maven和Gradle提供的传递依赖解析，以此实现Spring应用程序上下文里的自动配置。
从本质上来说，Spring Boot就是Spring，它做了那些没有它你自己也会去做的Spring Bean配置。
 
 
Spring Boot 入门
1.安装 Spring Boot CLI
 （1）用下载的分发包进行安装。
 （2）用Groovy Environment Manager进行安装。
 （3）通过OS X Homebrew进行安装。
 （4）使用MacPorts进行安装。
2.使用 Spring Initializr 初始化 Spring Boot 项目
 （1）通过Web界面使用：https://start.spring.io/
 （2）通过Spring Tool Suite使用。
 （3）通过IntelliJ IDEA使用。
 （4）使用Spring Boot CLI使用：参数控制
3.初始化 Spring Boot 项目文件，以gradle为例
  build.gradle： Gradle构建说明文件。如果选择Maven项目，这就会换成pom.xml。
  Application.java：一个带有main()方法的类，用于引导启动应用程序。
  ApplicationTests.java：一个空的JUnit测试类，它加载了一个使用Spring Boot自动配置功能的Spring应用程序上下文。
  application.properties：一个空的properties文件，你可以根据需要添加配置属性。
 
 
注解：@SpringBootApplication
@SpringBootApplication开启了Spring的组件扫描和Spring Boot的自动配置功能。实际上， 
@SpringBootApplication将三个有用的注解组合在了一起。
1.Spring的@Configuration：标明该类使用Spring基于Java的配置。虽然本书不会写太多
  配置，但我们会更倾向于使用基于Java而不是XML的配置。
2.Spring的@ComponentScan：启用组件扫描，这样你写的Web控制器类和其他组件才能被
  自动发现并注册为Spring应用程序上下文里的Bean。本章稍后会写一个简单的Spring MVC
  控制器，使用@Controller进行注解，这样组件扫描才能找到它。
3.Spring Boot的@EnableAutoConfiguration：这个不起眼的小注解也可以称为@Abracadabra①，
  就是这一行配置开启了Spring Boot自动配置的魔力，让你不用再写成篇的配置了。
Spring Boot的早期需要在启动类上注明这3个注解，但从Spring Boot 1.2.0开始，
有@SpringBootApplication就行了。
 
 
依赖：
我们并不需要指定版本号，起步依赖本身的版本是由正在使用的Spring Boot的版本来决定
的，而起步依赖则会决定它们引入的传递依赖的版本。
不知道自己所用依赖的版本，你多少会有些不安。你要有信心，相信Spring Boot经过了足够
的测试，确保引入的全部依赖都能相互兼容。
只需指定起步依赖，不用担心自己需要维护哪些库，也不必担心它们的版本。
如果真想查看依赖的各个版本，可以使用下面命令查看：
gradle dependencies 或者 mvn dependency:tree
 
 
起步依赖里所选的库仍有问题该怎么办？如何覆盖起步依赖呢？
可以通过构建工具中的功能，选择性地覆盖它们引入的传递依赖的版本号，排除传递依赖，
当然还可以为那些Spring Boot起步依赖没有涵盖的库指定依赖。
有时候排除掉一些不用的传递依赖，可以为你的项目瘦身。
1.假如你可能用不上Jackson，可以做如下操作：
compile("org.springframework.boot:spring-boot-starter-web") {
    exclude group: 'com.fasterxml.jackson.core'
}
或者
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
  <exclusions>
    <exclusion>
      <groupId>com.fasterxml.jackson.core</groupId>
    </exclusion>
  </exclusions>
</dependency>
2.假如你需要用另一个版本的Jackson 2.4.3来进行构建，而Web起步依赖引用了Jackson 2.3.4，
  此时会覆盖传递依赖引入的另一个依赖。
  注意：Gradle和Maven不太一样， Gradle倾向于使用库的最新版本。因此，如果你要使用老版
        本的Jackon，则不得不把老版本的依赖加入构建，并把Web起步依赖传递依赖的那个版本
        排除掉
compile("com.fasterxml.jackson.core:jackson-databind:2.4.3")  # 如果2.4.3是最新版本的话
或者
compile("org.springframework.boot:spring-boot-starter-web") {
  exclude group: 'com.fasterxml.jackson.core'
}
compile("com.fasterxml.jackson.core:jackson-databind:2.3.1")  # 老版本的话需先排除掉再引
或者
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.4.1</version>                                    # Maven总是会用最近的依赖
</dependency> 
注意：起步依赖中各个依赖版本之间的兼容性都经过了精心的测试。应该只在特殊的情况下覆盖这些
传递依赖（比如新版本修复了一个bug）
 
 
条件化配置允许配置存在于应用程序中，但在满足某些特定条件之前都忽略这个配置。Spring 4.0引入。
在Spring里可以很方便地编写你自己的条件，你所要做的就是实现Condition接口，覆盖它的matches()方法。
举例来说，下面这个简单的条件类只有在Classpath里存在JdbcTemplate时才会生效：
package readinglist;
import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;
 
public class JdbcTemplateCondition implements Condition {
  @Override
  public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
    try {
      context.getClassLoader().loadClass("org.springframework.jdbc.core.JdbcTemplate");
      return true;
    } catch (Exception e) {
      return false;
    }
  }
}
当你用Java来声明Bean的时候，可以使用这个自定义条件类：
@Conditional(JdbcTemplateCondition.class)
public MyService myService() {
  ...
}
在这个例子里，只有当JdbcTemplateCondition类的条件成立时才会创建MyService这个Bean。也就是
说MyService Bean创建的条件是Classpath里有JdbcTemplate。否则，这个Bean的声明就会被忽略掉。
Spring Boot定义了很多更有趣的条件，并把它们运用到了配置类上，这些配置类构成了Spring Boot的
自动配置，下面是一些条件化注解：
@ConditionalOnBean 配置了某个特定Bean
@ConditionalOnMissingBean 没有配置特定的Bean
@ConditionalOnClass Classpath里有指定的类
@ConditionalOnMissingClass Classpath里缺少指定的类
@ConditionalOnExpression 给定的Spring Expression Language（ SpEL）表达式计算结果为true
@ConditionalOnJava Java的版本匹配特定值或者一个范围值
@ConditionalOnJndi 参数中给定的JNDI位置必须存在一个，如果没有给参数，则要有JNDI InitialContext
@ConditionalOnProperty 指定的配置属性要有一个明确的值
@ConditionalOnResource Classpath里有指定的资源
@ConditionalOnWebApplication 这是一个Web应用程序
@ConditionalOnNotWebApplication 这不是一个Web应用程序
 
 
两种影响自动配置的方式——使用显式配置进行覆盖和使用属性进行精细化配置。
使用Spring Boot提供的钩子引入自定义的错误页。
1.想要覆盖Spring Boot的自动配置，你所要做的仅仅是编写一个显式的配置。
  Spring Boot会发现你的配置，随后降低自动配置的优先级，以你的配置为准。
2.如果你在同一优先级位置同时有application.properties和application.yml，那么application.
  yml里的属性会覆盖application.properties里的属性。
3.模板默认缓存。这有助于改善应用程序的性能，因为模板只需编译一次，不过开发时可以禁用缓存。
   spring.thymeleaf.cache （thymeleaf）
   spring.freemarker.cache（Freemarker）
   spring.groovy.template.cache（Groovy模板）
   spring.velocity.cache（Velocity）
4.嵌入式服务器，默认是tomcat并监听8080端口，修改端口可以修改server.port属性。
5.服务器提供HTTPS服务，第一步就是用JDK的keytool工具来创建一个密钥存储（keystore）生成mykeys.jks，
  记住填写的密码（这里是letmein），然后在application.properties配置
  server.port: 8443
  server.ssl.key-store: file:///path/to/mykeys.jks
  server.ssl.key-store-password: letmein
  server.ssl.key-password: letmein
  此处的server.port设置为8443，开发环境的HTTPS服务器大多会选这个端口。
  这里用了一个file://开头的URL从文件系统里加载该文件。也可以把它打包在应用程序的JAR文件里，
  用classpath: URL来引用它。 
  有了这些属性，应用程序就能在8443端口上监听HTTPS请求了（可能会出现警告框提示该服务器无法验证
  其身份。在开发时，访问的是localhost，这没什么好担心的。）
6.配置日志
  默认情况下， Spring Boot会用Logback来记录日志，并用INFO级别输出到控制台。
  一般来说，你不需要切换日志实现； Logback能很好地满足你的需要。但是，如果决定使
  用Log4j或者Log4j2，那么你只需要修改依赖，引入对应该日志实现的起步依赖，同时排除掉
  Logback。
  Maven:
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <exclusions>
      <exclusion>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-logging</artifactId>
      </exclusion>
    </exclusions>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j</artifactId>
  </dependency>
  Gradle:
  configurations {
    all*.exclude group:'org.springframework.boot',
    module:'spring-boot-starter-logging'
  }
  compile("org.springframework.boot:spring-boot-starter-log4j")
  默认情况下，日志文件的大小达到10MB时会切分一次。
7.应用程序 Bean 的配置外置
  Controller上加了@ConfigurationProperties注解,通过setter方法从配置属性值注入.
  amazon.associateId=habuma-20
  amazon.associateId 这个属性和amazon.associate_id以及amazon.associate-id都是等价的.
 
  Spring Framework从Spring 3.1开始支持基于Profile的配置。 Profile是一种条件化配置，
  基于运行时激活的Profile，会使用或者忽略不同的Bean或配置类。
  @Profile("production")
  @Configuration
  @EnableWebSecurity
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
      ...
  }
  spring.profiles.active: production
  或运行时添加参数
  java -jar readinglist-0.0.1-SNAPSHOT.jar --spring.profiles.active=production
8.使用特定于Profile的属性文件
  如果你正在使用application.properties，可以创建额外的属性文件，遵循application-{profile}.
  properties这种命名格式，这样就能提供特定于Profile的属性了。
  在日志配置为例子，开发环境的配置可以放在名为application-development.properties的文件
  里，配置包含日志级别和输出到控制台：logging.level.root=DEBUG
  对于生产环境， application-production.properties会将日志级别设置为WARN或更高级别，并将
  日志写入日志文件：logging.level.root=WARN
  与此同时，那些并不特定于哪个Profile或者保持默认值（以防万一有哪个特定于Profile的配
  置不指定这个值）的属性，可以继续放在application.properties里：logging.level.root=INFO
  yml可以和上面一样等同配置,不过yml还可以以---分割放到一个文件进行配置,如将下面内容放到
  application.yml一个文件里
  logging:
    level:
      root: INFO
  ---
  spring:
    profiles: development
  logging:
    level:
      root: DEBUG
  ---
  spring:
    profiles: production
  logging:
    level:
      root: WARN
9.自定义的错误页
  这个自定义的错误模板应该命名为error.html，放在模板目录里，这样Thymeleaf模板解析器
  才能找到它。在典型的Maven或Gradle项目里，这就意味着要把该文件放在src/main/resources/
  templates中，运行时它就在Classpath的根目录里。
  Spring Boot会为错误视图提供如下错误属性:
  timestamp：错误发生的时间。
  status： HTTP状态码。
  error：错误原因。
  exception：异常的类名。
  message：异常消息（如果这个错误是由异常引起的）。
  errors： BindingResult异常里的各种错误（如果这个错误是由异常引起的）。
  trace：异常跟踪信息（如果这个错误是由异常引起的）。
  path：错误发生时请求的URL路径。
 
 
 
第1章小结:
  Spring Boot为Spring应用程序的开发提供了一种激动人心的新方式，框架本身带来的阻力很
小。自动配置消除了传统Spring应用程序里的很多样板配置； Spring Boot起步依赖让你能通过库
所提供的功能而非名称与版本号来指定构建依赖； Spring Boot CLI将Spring Boot的无阻碍开发模
型提升到了一个崭新的高度，在命令行里就能简单快速地用Groovy进行开发； Actuator让你能深
入运行中的应用程序，了解Spring Boot做了什么，是怎么做的。
 
 
第2章小结:
  通过Spring Boot的起步依赖和自动配置，你可以更加快速、便捷地开发Spring应用程序。起步依
赖帮助你专注于应用程序需要的功能类型，而非提供该功能的具体库和版本。与此同时，自动配置把
你从样板式的配置中解放了出来。这些配置在没有Spring Boot的Spring应用程序里非常常见。
 
 
第3章小结:
  Spring Boot消除了Spring应用程序中经常要用到的很多样板式配置。让Spring Boot处理全部配置，
你可以仰仗它来配置那些适合你的应用程序的组件。当自动配置无法满足需求时， SpringBoot允许你
覆盖并微调它提供的配置。
  覆盖自动配置其实很简单，就是显式地编写那些没有Spring Boot时你要做的Spring配置。Spring 
Boot的自动配置被设计为优先使用应用程序提供的配置，然后才轮到自己的自动配置。
  即使自动配置合适，你仍然需要调整一些细节。 Spring Boot会开启多个属性解析器，让你通过环
境变量、属性文件、 YAML文件等多种方式来设置属性，以此微调配置。这套基于属性的配置模型也能
用于应用程序自己定义的组件，可以从外部配置源加载属性并注入到Bean里。
  Spring Boot还自动配置了一个简单的白标错误页，虽然它比异常跟踪信息友好一点，但在艺术性方
面还有很大的提升空间。幸运的是， Spring Boot提供了好几种选项来自定义或完全替换这个白标错
误页，以满足应用程序的特定风格。
 
 
 
 
 
 
 
 
 
 
 
附录Error：
1.javax.management.InstanceNotFoundException: org.springframework.boot:type=Admin,name=SpringApplication
ref：https://stackoverflow.com/questions/50436108/javax-management-instancenotfoundexception-org-springframework-boottype-admin
idea：remove the 'Enable launch optimization' and 'Enable JMX agent' checkboxes when Edit Configurations
 
2.Inferred type 'S' for type parameter 'S' is not within its bound;
ref：https://blog.csdn.net/HeatDeath/article/details/79840834
UserDetails userDetails = readerRepository.findById(username).orElse(null);
 
3.import org.springframework.boot.autoconfigure.web.DefaultErrorAttributes
ref：https://blog.csdn.net/qq_41971768/article/details/109597242
replace to：org.springframework.boot.web.servlet.error.DefaultErrorAttributes
 
4.java.lang.ClassNotFoundException: java.lang.reflect.InaccessibleObjectException
ref：https://blog.csdn.net/weixin_41561929/article/details/109345347
upgrade to java9+
 
5.org.apache.tomcat.jni.LibraryNotFoundError: Can't load library: D:\idea\workspace\ch03\bin\tcnative-1.dll, Can't load library: D:\idea\workspace\ch03\bin\libtcnative-1.dll, no tcnative-1 in java.library.path, no libtcnative-1 in java.library.path
ref：https://blog.csdn.net/m0_37460012/article/details/100749271
copy apache-tomcat-8.5.43\bin\tcnative-1.dll to C:\Windows\System32
 
6.java.lang.ClassNotFoundException: java.net.UnixDomainSocketAddress
ref：https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/net/UnixDomainSocketAddress.html
change to jdk16
 
7.java.lang.IllegalArgumentException: There is no PasswordEncoder mapped for the id "null"
ref：https://blog.csdn.net/Hello_World_QWP/article/details/81811462
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.userDetailsService(new UserDetailsService() {
        @Override
        public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
            UserDetails userDetails = readerRepository.findById(username).orElse(null);
            if (userDetails != null) {
                return userDetails;
            }
            throw new UsernameNotFoundException("User '" + username + "' not found.");
        }
    }).passwordEncoder(new PasswordEncoder() {
        @Override
        public String encode(CharSequence rawPassword) {
            return rawPassword.toString();
        }
 
        @Override
        public boolean matches(CharSequence rawPassword, String encodedPassword) {
            return encodedPassword.equals(rawPassword.toString());
        }
    });
}
 
使用spring boot security自动配置的登录有些问题,这个暂时没看
因为我使用的是intellj idea2018.2.4,对老的gradle版本不支持,所以使用的是
gradle-5.6.1
jdk-16.0.1 (主要是因为报UnixDomainSocketAddress不存在,所以升到16,仅供学习,目前一般公司用的基本还是jdk8或11)
```
