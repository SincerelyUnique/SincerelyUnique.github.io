---
title: spring boot发送短信服务
date: 2017-09-27 08:39:59
tags:
- spring
- email
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.stereotype.Service;
import org.springframework.util.LinkedMultiValueMap;
import org.springframework.util.MultiValueMap;
import org.springframework.web.client.RestTemplate;
 
import java.text.SimpleDateFormat;
import java.util.Date;
 
/**
* Created by jalen on 17-6-1.
*/
@Service
public class SmsService {
    @Autowired
    RestTemplate restTemplate;
    @Value("${sms.username}")
    String username;
    @Value("${sms.password}")
    String password;
    @Value("${sms.url}")
    String url;
 
    public String sendMsg(String phoneNum,String text){
        if (text.getBytes().length<=500){
            String postUrl = url + "xxxx";
 
            HttpHeaders headers = new HttpHeaders();
            headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);
 
            MultiValueMap<String,String> p = new LinkedMultiValueMap<>();
            p.add("userId",username);
            p.add("password",password);
            p.add("pszMobis",phoneNum);
            p.add("pszMsg",text);
 
            HttpEntity< MultiValueMap<String,String>> entity = new HttpEntity< MultiValueMap<String,String>>(p,headers);
 
            String result = restTemplate.postForObject(postUrl,entity,String.class);
 
            return result;
        }else {
            String result = "短信内容过长，请重新编辑";
            return result;
        }
    }
 
}
```

看注入的属性RestTemplate对象实在哪里实例化的，这里实在spring boot入口Application处设置的bean

```java
import com.xxx.repository.support.CustomRepository;
import com.ulisesbocchio.jasyptspringboot.annotation.EnableEncryptableProperties;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.web.client.RestTemplate;
 
@SpringBootApplication
//@EnableEncryptableProperties    //启用配置文件加密Encrypt
@EnableJpaAuditing
//@EnableCaching      //启用Ehcache缓存
@EnableJpaRepositories(repositoryBaseClass = CustomRepository.class)
public class Application {
 
  @Bean
  RestTemplate restTemplate(){
      return new RestTemplate();
  }
 
  public static void main(String[] args) {
      SpringApplication.run(Application.class, args);
  }
}
```

@Value("${sms.username}")中值的获取，在配置文件yml中设置的

```yaml
sms:
  username: xxxx
  password: xxxx
  url: http://xx.xx.xx.xx:8086/xx/xx.asmx/
```
