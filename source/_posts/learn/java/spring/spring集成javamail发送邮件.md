---
title: spring集成javamail发送邮件
date: 2017-12-22 12:08:05
tags:
- spring
- java
categories:
- [学习, Java语言学习, Spring]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 依赖

## spring基本jar包

```xml
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context-support</artifactId>
      <version>4.3.8.RELEASE</version>
    </dependency>
```

## javamail

```xml
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4</version>
    </dependency>
```

# 配置bean

```xml
    <!-- spring mail -->
    <bean id="mailSender" class="org.springframework.mail.javamail.JavaMailSenderImpl">
        <property name="host" value="smtp.qq.com" />
        <property name="username" value="你的QQ邮箱，如xxx@qq.com" />
        <property name="password" value="你的QQ邮箱授权码，自己去登录邮箱，从设置->账户->开通pops/smtp服务，生成,不是你的qq登录密码" />
        <property name="javaMailProperties">
            <props>
                <prop key="mail.smtp.auth">true</prop>
                <prop key="mail.smtp.timeout">30000</prop>
                <prop key="mail.debug">true</prop>
                <prop key="mail.smtp.starttls.enable">true</prop>
                <!--<prop key="mail.smtp.socketFactory.class">javax.net.ssl.SSLSocketFactory</prop>-->
            </props>
        </property>
    </bean>
```

# 使用

```java
    @Autowired
    private JavaMailSender mailSender;
 
    @PostMapping("sendEmail")
    @ResponseBody
    public ResponseVo<String> sendEmail( HttpServletRequest request ){
        try {
            // 获取参数
            String senderAddress = request.getParameter("senderAddress");
            String recipientAddress = request.getParameter("recipientAddress");
            String subject = request.getParameter("subject");
            String message = request.getParameter("message");
 
            // 创建邮件
            SimpleMailMessage email = new SimpleMailMessage();
            email.setFrom(senderAddress);
            email.setTo(recipientAddress);
            email.setSubject(subject);
            email.setText(message);
 
            // 发送邮件
            mailSender.send(email);
 
            return new ResponseVo<String>(true,"10000","发送成功",null);
        }catch (Exception e){
            log.error("user/sendEmail接口异常",e);
            return new ResponseVo<String>(true,"10001","邮件发送失败，请联系系统管理员",null);
        }
    }
```

如果想用邮件模板，可以引入freemarker

附录：

也可以利用github封装好的工具

参考：https://github.com/biezhi/oh-my-email
