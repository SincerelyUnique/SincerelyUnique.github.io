---
title: 阅读笔记(4，5章节)
date:  2021-06-01 13:34:03
categories:
- [学习, Java语言学习, SpringBoot实战]
tags:
- java
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```bash
《Spring Boot in action》阅读笔记(4，5章节)
 
 
中文名：Spring Boot实战
英文名：Spring Boot in action
作者：Craig Walls
译者：丁雪丰
出版社：人民邮电出版社2016年9月第1版
随书源码：www.manning.com/books/spring-boot-in-action
注：随书源码版本比较老，可以按自己的来
 
 
4.测试
  究竟是在编写业务代码之前还是之后写测试，这并不重要。重要的是，写测试不仅仅是为了
验证代码的准确性，还要确认它符合预期。测试也是一道保障，确认应用程序在改进的同时不会
破坏已有的东西。
  在编写单元测试的时候， Spring通常不需要介入。 Spring鼓励松耦合、接口驱动的设计，这
些都能让你很轻松地编写单元测试。但是在写单元测试时并不需要用到Spring。
  但是，集成测试要用到Spring。如果生产应用程序使用Spring来配置并组装组件，那么测试
就需要用它来配置并组装那些组件。
 
 
4.1 集成测试自动配置
  Spring Framework的核心工作是将所有组件编织在一起，构成一个应用程序。整个过程就是
读取配置说明.在应用程序上下文里初始化Bean，将Bean注入依赖它们的其他Bean中。
  Spring自1.1.1版就向集成测试提供了极佳的支持。自Spring 2.5开始，集成测试支持的形式就
变成了SpringJUnit4ClassRunner。Spring提供了额外的辅助功能，比如组件扫描、自动织入和声明
性切面（缓存、事务和安全，等等）。
  注意从Spring Boot 1.4以后有些注解不再使用,改为直接使用@SpringBootTest,可以搭配JUnit4
的注解@RunWith(SpringJUnit4ClassRunner.class)一块使用,搭配@RunWith(SpringRunner.class)也可以.
  Spring的Mock MVC取代了实际的Servlet容器。这样做的好处是测试方法运行相对较快。因为不需要等
待服务器启动，而且不需要打开Web浏览器发送表单，所以测试比较简单快捷.
  Spring Boot支持将Tomcat或Jetty这样的嵌入式Servlet容器作为运行中的应用程序的一部分，可以运
用相同的机制，在测试过程中用嵌入式Servlet容器来启动应用程序。
  Spring Boot支持用随机端口启动服务器.
  RestTemplate对于简单的请求而言使用方便，是测试REST端点的理想工具。不过不方便对页面内容或者
页面上执行的操作进行断言。
  用Selenium测试阅读列表应用程序，让我们先写一个测试来获取首页，为新书填写表单，提交表单，
随后判断返回的页面里是否包含新添加的图书。Selenium还提供了不少其他浏览器的驱动，包括IE、 
Google的Chrome，还有Apple的Safari。可以模拟敲击键盘事件等.
 
4.小结
  测试是开发高质量软件的重要一环。没有好的测试，你永远无法保证应用程序能像期望的那
样运行。
  单元测试专注于单一组件或组件中的一个方法，此处并不一定要使用Spring。 Spring提供了
一些优势和技术——松耦合、依赖注入和接口驱动设计。这些都简化了单元测试的编写。但Spring
不用直接涉足单元测试。
  集成测试会涉及众多组件，这时就需要Spring帮忙了。实际上，如果Spring在运行时负责拼
装那些组件，那么Spring在集成测试里同样应该肩负这一职责。
  Spring Framework以JUnit类运行器的方式提供了集成测试支持， JUnit类运行器会加载Spring
应用程序上下文，把上下文里的Bean注入测试。 Spring Boot在Spring的集成测试之上又增加了配置
加载器，以Spring Boot的方式加载应用程序上下文，包括了对外置属性的支持和Spring Boot日志。
  Spring Boot还支持容器内测试Web应用程序，让你能用和生产环境一样的容器启动应用程序。
这样一来，测试在验证应用程序行为的时候，会更加接近真实的运行环境。
 
 
 
5.Groovy与Spring Boot CLI
包括
(1)自动依赖与import
(2)获取依赖
(3)测试基于CLI的应用程序
 
 
5.1 开发 Spring Boot CLI 应用程序
  最近版本的Java语言有不少改进。然而，即便如此， Java还是有一些严格的规则为代码增加
了不少噪声。行尾分号、类和方法的修饰符（比如public和private）、 getter和setter方法，
还有import语句在Java中都有自己的作用，但它们同代码的本质无关，因而造成了干扰。从开发
者的角度来看，代码噪声是阻力——编写代码时是阻力，试图阅读代码时更是阻力。如果能消除
一部分代码噪声，代码的开发和阅读可以更加方便。
  在使用Spring Boot CLI时，没有构建说明文件。代码本身就是构建说明，提供线索指引CLI
解析依赖，并生成用于部署的产物。消除了几乎所有代码噪声.
  与基于Maven或Gradle的项目不同， Spring Boot CLI项目并没有严格的项目结构要求。实际
上，最简单的Spring Boot CLI应用程序就是一个Groovy脚本，可以放在文件系统的任意目录里。
  Groovy并不要求有public和private这样的限定符，也不要求在行尾有分号。此外，归功于
Groovy的简化属性语法（GroovyBeans）， JavaBean的标准访问方法没有存在的必要了。Groovy
类与它的Java类相比，大小完全不在一个量级。这里没有setter和getter方法，没有Spring配置,
没有import语句,没有部署应用.
 
 
发生了什么?
(1)CLI可以利用Spring Boot的自动配置和起步依赖。
(2)CLI可以检测到正在使用的特定类，自动解析合适的依赖库来支持那些类。
(3)CLI知道多数常用类都在哪些包里，如果用到了这些类，它会把那些包加入Groovy的默认包里。
(4)应用自动依赖解析和自动配置后， CLI可以检测到当前运行的是一个Web应用程序，并自动引入
   嵌入式Web容器（默认是Tomcat）供应用程序使用。
这些才是CLI提供的最重要的特性。 Groovy语法只是额外的福利！
CLI尝试用内嵌的Groovy编译器来编译Groovy代码。
 
 
  @Grab注解的作用是方便添加CLI无法自动解析的依赖。虽然它看上去很简单，但实际上这个小小
的注解作用远比你想象得要大。为了便于组织管理，最好创建一个空类，把所有@Grab注解放在一起。
@Grab注解源自 Groovy Grape工具。从本质上来说， Grape允许Groovy脚本在运行时下载依赖，无
需Maven或Gradle这样的构建工具介入。除了支持@Grab注解， Spring Boot CLI还用Grape来获取代
码中推断出的依赖。如:
  @Grab(group="com.h2database", module="h2", version="1.4.190")
  @Grab("com.h2database:h2:1.4.185")  //使用:号分割形式简单的写
  @Grab("com.h2database:h2")   //很多依赖不再要求指定版本号
  @Grab("h2")  //很多常用依赖还可以省去Group ID，直接在@Grab里写上模块的ID
 
 
覆盖默认依赖版本
Spring Boot引入了新的@GrabMetadata注解，可以和@Grab搭配使用，用属性文件里的内容
来覆盖默认的依赖版本。
如: @GrabMetadata("com.myorg:custom-versions:1.0.0")
会从Maven仓库的com/myorg目录里加载一个名为custom-versions.properties的文件。文件里的每
一行都应该有Group ID和Module ID。以这两个东西为键名，属性则是值。例如，要把H2的默认版本
覆盖为1.4.186，可以把@GrabMetadata指向一个包含如下内容的属性文件：
  com.h2database:h2=1.4.186
默认情况下， @Grab声明的依赖是从Maven中心仓库（http://repo1.maven.org/maven2/）拉取的。
 
 
@GrabResolver注解可以让你指定额外的仓库，用来获取依赖。
@GrabResolver(name='jboss', root='https://repository.jboss.org/nexus/content/groups/public-jboss')
这里通过name属性将该解析器命名为jboss，通过root属性来指定仓库的URL。
 
 
用CLI运行测试
$ spring test tests/ReadingListControllerTest.groovy
不过在Spring Boot2.0 test命令remove掉了
参考:
https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide#removed-features
https://stackoverflow.com/questions/60369964/spring-cli-not-found-test-command 
原因: “once an app gets to the stage of requiring a test suite, it should be converted to a Maven or Gradle project”
 
 
用CLI创建可部署的产物
$ spring jar ReadingList.jar 
$ java -jar ReadingList.jar
 
 
5.小结
  Spring Boot CLI利用了Spring Boot自动配置和起步依赖的便利之处，并将其发扬光大。
借由Groovy语言的优雅， CLI能让我们在最少的代码噪声下开发Spring应用程序.
  对于CLI无法自动解析的库，基于CLI的应用程序可以利用Grape的@Grab注解，不用构建说
明也能显式地声明依赖。 Spring Boot的CLI扩展了@Grab注解，针对很多常用库依赖，只
需声明Module ID就可以了。
 
 
6.在Spring Boot中使用Grails
(1)使用GORM持久化数据
(2)定义GSP视图
(3)Grails 3和Spring Boot入门
这一章主要是基于groovy,因为书中代码版本兼容性问题,先略过
 
 
 
 
 
 
 
 
附录:
1.h2可视化工具
  参考: https://www.cnblogs.com/flydean/p/12680291.html
2.Caused by: java.lang.IllegalArgumentException: Could not resolve placeholder 'local.server.port' in value "${local.server.port}"
  参考: https://stackoverflow.com/questions/43491893/unable-to-set-runtime-local-server-port-in-spring-boot-test-1-5
  修改: @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
3.java.lang.IllegalStateException: The path to the driver executable must be set by the webdriver.gecko.driver system property; for more information, see https://github.com/mozilla/geckodriver. The latest version can be downloaded from https://github.com/mozilla/geckodriver/releases
  参考: https://stackoverflow.com/questions/38676719/selenium-using-java-the-path-to-the-driver-executable-must-be-set-by-the-webdr
  首先去git下载geckodriver.exe
  添加静态代码块,初始化执行: System.setProperty("webdriver.gecko.driver", "path/to/geckodriver.exe");
4.Intellij Idea报 Groovy SDK is not configured for module 'xxx'
  下载解压: https://groovy.apache.org/download.html
5.D:\idea\workspace\ch05\readinglist>spring run .  报 Cannot create URL from path []
  参考: https://stackoverflow.com/questions/46749020/getting-cannot-create-url-from-path-in-spring-example
  正确执行应当是: spring run *.*   (说是spring boot in action书籍的印刷或者是作者的一个失误)
```
