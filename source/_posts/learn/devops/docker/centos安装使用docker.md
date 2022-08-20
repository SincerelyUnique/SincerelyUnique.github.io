---
title: centos安装使用docker
date: 2018-04-18 17:21:05
tags:
- centos
- docker
categories:
- [学习, DevOPS, Docker]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 安装docker，最好centos版本7.x（不是可重装或参考网上6.5安装教程），linux内核3.1+（不是可升级内核），docker安装详细可参考菜鸟教程

（1）查看linux版本

```bash
[root@jalen demo-service-hi]# lsb_release -a
LSB Version:	:core-4.1-amd64:core-4.1-noarch
Distributor ID:	CentOS
Description:	CentOS Linux release 7.4.1708 (Core) 
Release:	7.4.1708
Codename:	Core
```

（2）查看内核版本

```bash
[root@jalen demo-service-hi]# uname -r
3.10.0-693.2.2.el7.x86_64
```

（3）安装docker

```bash
[root@jalen demo-service-hi]# yum -y install docker
```

（4）关闭/启动docker，也可使用systemd操作

```bash
[root@jalen demo-service-hi]# service docker stop
Redirecting to /bin/systemctl stop docker.service
[root@jalen demo-service-hi]# service docker start
Redirecting to /bin/systemctl start docker.service
```

2. 以spring cloud项目为例，进入maven项目根目录

（1）打包镜像

```bash
[root@jalen ~]# cd /usr/local/jalen/tomcat/demo-eureka-server/
[root@jalen demo-eureka-server]# ls
demo-eureka-server.iml  mvnw  mvnw.cmd  pom.xml  src  target
[root@jalen demo-eureka-server]# mvn clean package docker:build
```

（2）如果服务器购自类似搬瓦工等国外运营商，无需加速；

（3）如果是国内阿里云等，需做docker加速处理，否则超慢，可从DaoCloud获取镜像，将镜像链接添加到 /etc/docker/daemon.json文件中

3. 查看当前打包好的docker镜像，这里我打了两个，jalen/*-*-*

```bash
[root@jalen demo-service-hi]# docker images
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
jalen/demo-service-hi                  latest              8143462b6e72        10 seconds ago      203 MB
jalen/demo-eureka-server               latest              9ea3aae87063        2 minutes ago       208 MB
<none>                                 <none>              d27ae327e5be        55 minutes ago      208 MB
docker.io/frolvlad/alpine-oraclejdk8   slim                afe006594870        2 weeks ago         167 MB
```

4. 运行镜像，-p指后台运行

```bash
[root@jalen demo-eureka-server]# docker run -p 8761:8761 -t jalen/demo-eureka-server
[root@jalen demo-eureka-server]# docker run -p 8763:8763 -t jalen/demo-service-hi
```

5. 页面访问地址，47.98.242.28是我的阿里云地址，8761为服务注册中心，8763为一个服务实例

http://47.98.242.28:8761/

http://47.98.242.28:8763/hi?name=jalen

6. 查看当前服务器正在运行的docker镜像

```bash
[root@jalen demo-eureka-server]# docker ps
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                    NAMES
6c8edaed2497        jalen/demo-service-hi      "java -Djava.secur..."   2 minutes ago       Up 2 minutes        0.0.0.0:8763->8763/tcp   jolly_visvesvaraya
0635e97ec951        jalen/demo-eureka-server   "java -Djava.secur..."   3 minutes ago       Up 3 minutes        0.0.0.0:8761->8761/tcp   dreamy_mcnulty
```

项目代码参考，可以使用7z打成tar.gz包到云上再执行以上操作

https://github.com/SincerelyUnique/spring-cloud-demo/tree/master/11docker%20deploy

7. 除以上方法外，还可以使用docker-compose命令部署，一次直接部署多个镜像

```
[root@jalen 12docker deploy compose]# docker-compose up
Creating network "12dockerdeploycompose_default" with the default driver
Creating 12dockerdeploycompose_eureka-server_1 ... done
Creating 12dockerdeploycompose_service-hi_1    ... done
Attaching to 12dockerdeploycompose_eureka-server_1, 12dockerdeploycompose_service-hi_1
eureka-server_1  | 2018-04-19 01:06:37.923  INFO 1 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@1376c05c: startup date [Thu Apr 19 01:06:37 GMT 2018]; root of context hierarchy
service-hi_1     | 2018-04-19 01:06:37.937  INFO 1 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@1376c05c: startup date [Thu Apr 19 01:06:37 GMT 2018]; root of context hierarchy
eureka-server_1  | 2018-04-19 01:06:40.394  INFO 1 --- [           main] f.a.AutowiredAnnotationBeanPostProcessor : JSR-330 'javax.inject.Inject' annotation found and supported for autowiring
service-hi_1     | 2018-04-19 01:06:40.508  INFO 1 --- [           main] f.a.AutowiredAnnotationBeanPostProcessor : JSR-330 'javax.inject.Inject' annotation found and supported for autowiring
service-hi_1     | 2018-04-19 01:06:40.972  INFO 1 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'configurationPropertiesRebinderAutoConfiguration' of type [org.springframework.cloud.autoconfigure.ConfigurationPropertiesRebinderAutoConfiguration$$EnhancerBySpringCGLIB$$7e4594e4] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
eureka-server_1  | 2018-04-19 01:06:40.985  INFO 1 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'configurationPropertiesRebinderAutoConfiguration' of type [org.springframework.cloud.autoconfigure.ConfigurationPropertiesRebinderAutoConfiguration$$EnhancerBySpringCGLIB$$7e4594e4] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
eureka-server_1  | 
eureka-server_1  |   .   ____          _            __ _ _
eureka-server_1  |  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
eureka-server_1  | ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
eureka-server_1  |  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
eureka-server_1  |   '  |____| .__|_| |_|_| |_\__, | / / / /
eureka-server_1  |  =========|_|==============|___/=/_/_/_/
eureka-server_1  |  :: Spring Boot ::        (v1.5.2.RELEASE)
eureka-server_1  | 
eureka-server_1  | 2018-04-19 01:06:43.650  INFO 1 --- [           main] c.e.eureka.server.demo.DemoApplication   : No active profile set, falling back to default profiles: default
eureka-server_1  | 2018-04-19 01:06:43.752  INFO 1 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@3a82f6ef: startup date [Thu Apr 19 01:06:43 GMT 2018]; parent: org.springframework.context.annotation.AnnotationConfigApplicationContext@1376c05c
service-hi_1     | 
service-hi_1     |   .   ____          _            __ _ _
service-hi_1     |  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
service-hi_1     | ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
service-hi_1     |  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
service-hi_1     |   '  |____| .__|_| |_|_| |_\__, | / / / /
service-hi_1     |  =========|_|==============|___/=/_/_/_/
service-hi_1     |  :: Spring Boot ::        (v1.5.2.RELEASE)
service-hi_1     | 
service-hi_1     | 2018-04-19 01:06:43.991  INFO 1 --- [           main] c.e.service.hi.demo.DemoApplication      : No active profile set, falling back to default profiles: default
service-hi_1     | 2018-04-19 01:06:44.102  INFO 1 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@6d00a15d: startup date [Thu Apr 19 01:06:44 GMT 20
```

8. 如果想要使用docker-compose后台部署，可以

```bash
[root@jalen 12docker deploy compose]# docker-compose up -d
```

项目代码参考

https://github.com/SincerelyUnique/spring-cloud-demo/tree/master/12docker%20deploy%20compose
