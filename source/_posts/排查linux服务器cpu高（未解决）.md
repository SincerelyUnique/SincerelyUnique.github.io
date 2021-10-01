---
title: 排查linux服务器cpu高（未解决）
date: 2019-10-28 17:23:13
tags:
- linux
- cpu
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```
1. top：发现2655进程cpu使用率高
[root@myservername jalchu]# top
top - 01:15:16 up 8 days, 20:52,  1 user,  load average: 0.06, 0.03, 0.01
Tasks: 139 total,   1 running, 138 sleeping,   0 stopped,   0 zombie
Cpu(s):  8.4%us,  6.8%sy,  0.0%ni, 83.4%id,  0.1%wa,  0.0%hi,  1.3%si,  0.0%st
Mem:  12330220k total,  8314340k used,  4015880k free,   717840k buffers
Swap:  4079612k total,        0k used,  4079612k free,   902748k cached
 
  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                                                                                                               
 2655 nobody    20   0 9219m 5.4g  18m S 49.1 46.1   4100:06 jsvc                                                                                                                   
 4587 root      20   0 28032 1544 1152 R  1.8  0.0   0:00.05 top                                                                                                                    
    1 root      20   0 30192 1768 1424 S  0.0  0.0   0:02.47 init                                                                                                                   
    2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd                                                                                                               
    3 root      RT   0     0    0    0 S  0.0  0.0   0:01.25 migration/0                                                                                                            
    4 root      20   0     0    0    0 S  0.0  0.0   0:05.05 ksoftirqd/0   
 
 
2. process：发现该进程是java进程，同时是一个tomcat进程
[root@myservername jalchu]# ps -elf|grep 2655
5 S nobody    2655  2654 32  80   0 - 2361249 hrtime Oct16 ?      2-20:19:03 jsvc.exec -java-home /usr/lib/jvm/jre -user nobody -pidfile /opt/apache-tomcat_1/logs/catalina-daemon.pid -umask 0007 -wait 10 -errfile &1 -classpath /opt/apache-tomcat_1/bin/bootstrap.jar:/opt/apache-tomcat_1/bin/commons-daemon.jar:/opt/apache-tomcat_1/bin/tomcat-juli.jar -Djava.util.logging.config.file=/opt/apache-tomcat_1/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Dorg.apache.catalina.security.SecurityListener.UMASK=0007 -Dorg.apache.catalina.STRICT_SERVLET_COMPLIANCE=true -Dorg.apache.catalina.connector.RECYCLE_FACADES=false -Dorg.apache.catalina.connector.CoyoteAdapter.ALLOW_BACKSLASH=false -Dorg.apache.tomcat.util.buf.UDecoder.ALLOW_ENCODED_SLASH=false -Dorg.apache.coyote.USE_CUSTOM_STATUS_MSG_IN_HEADER=false -Dorg.apache.catalina.connector.Response.ENFORCE_ENCODING_IN_GET_WRITER=true -Dsun.net.inetaddr.ttl=30 -Dcom.sun.management.config.file=/opt/apache-tomcat_1/jmxconf/jvmmgmt/management.properties -javaagent:/opt/webex/webapps/appdynamics/javaagent.jar -Xms6144m -Xmx6144m -XX:MaxPermSize=1024m -Xmn2048M -XX:+UseConcMarkSweepGC -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -Dorg.apache.tomcat.util.http.ServerCookie.ALLOW_EQUALS_IN_VALUE=true -Dorg.apache.tomcat.util.http.ServerCookie.FWD_SLASH_IS_SEPARATOR=false -Dhttps.protocols=TLSv1,TLSv1.1,TLSv1.2 -Dignore.endorsed.dirs= -Dcatalina.base=/opt/apache-tomcat_1 -Dcatalina.home=/opt/apache-tomcat_1 -Djava.io.tmpdir=/opt/apache-tomcat_1/temp -Djava.security.manager -Djava.security.policy==/opt/apache-tomcat_1/conf/catalina.policy org.apache.catalina.startup.Bootstrap
4 S root      3670  3393  0  80   0 - 25829 pipe_w 01:13 pts/0    00:00:00 grep 2655
[root@myservername jalchu]#
 
 
4. thread：发现该进程下4658线程cpu使用率很高
[root@myservername jalchu]# top -H -p 2655
top - 01:16:35 up 8 days, 20:54,  1 user,  load average: 0.01, 0.02, 0.00
Tasks: 146 total,   0 running, 146 sleeping,   0 stopped,   0 zombie
Cpu(s): 12.2%us, 12.1%sy,  0.0%ni, 73.3%id,  0.0%wa,  0.0%hi,  2.3%si,  0.0%st
Mem:  12330220k total,  8314492k used,  4015728k free,   717840k buffers
Swap:  4079612k total,        0k used,  4079612k free,   902880k cached
 
  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                                                                                                               
 4658 nobody    20   0 9219m 5.4g  18m S 23.9 46.1   1728:30 jsvc                                                                                                                   
 4675 nobody    20   0 9219m 5.4g  18m S  6.0 46.1 456:52.88 jsvc                                                                                                                   
 4659 nobody    20   0 9219m 5.4g  18m S  5.7 46.1 398:57.20 jsvc                                                                                                                   
 4667 nobody    20   0 9219m 5.4g  18m S  5.3 46.1 381:28.58 jsvc                                                                                                                   
 4661 nobody    20   0 9219m 5.4g  18m S  4.7 46.1 318:53.51 jsvc                                                                                                                   
 4665 nobody    20   0 9219m 5.4g  18m S  4.3 46.1 320:46.85 jsvc                                                                                                                   
 4663 nobody    20   0 9219m 5.4g  18m S  3.7 46.1 272:35.07 jsvc                                                                                                                   
 2659 nobody    20   0 9219m 5.4g  18m S  0.3 46.1   8:00.41 jsvc                                                                                                                   
 2655 nobody    20   0 9219m 5.4g  18m S  0.0 46.1   0:02.33 jsvc                                                                                                                   
 2656 nobody    20   0 9219m 5.4g  18m S  0.0 46.1   4:16.10 jsvc                                                                                                                   
 2657 nobody    20   0 9219m 5.4g  18m S  0.0 46.1   4:16.06 jsvc                                                                                                                   
 2658 nobody    20   0 9219m 5.4g  18m S  0.0 46.1   0:58.78 jsvc        
 
 
其他check：
 
5. PS Java Process：
[root@myservername bin]# ps -elf|grep java
1 S root      2654     1  0  80   0 -  2608 do_wai Oct16 ?        00:00:00 jsvc.exec -java-home /usr/lib/jvm/jre -user nobody -pidfile /opt/apache-tomcat_1/logs/catalina-daemon.pid -umask 0007 -wait 10 -errfile &1 -classpath /opt/apache-tomcat_1/bin/bootstrap.jar:/opt/apache-tomcat_1/bin/commons-daemon.jar:/opt/apache-tomcat_1/bin/tomcat-juli.jar -Djava.util.logging.config.file=/opt/apache-tomcat_1/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Dorg.apache.catalina.security.SecurityListener.UMASK=0007 -Dorg.apache.catalina.STRICT_SERVLET_COMPLIANCE=true -Dorg.apache.catalina.connector.RECYCLE_FACADES=false -Dorg.apache.catalina.connector.CoyoteAdapter.ALLOW_BACKSLASH=false -Dorg.apache.tomcat.util.buf.UDecoder.ALLOW_ENCODED_SLASH=false -Dorg.apache.coyote.USE_CUSTOM_STATUS_MSG_IN_HEADER=false -Dorg.apache.catalina.connector.Response.ENFORCE_ENCODING_IN_GET_WRITER=true -Dsun.net.inetaddr.ttl=30 -Dcom.sun.management.config.file=/opt/apache-tomcat_1/jmxconf/jvmmgmt/management.properties -javaagent:/opt/webex/webapps/appdynamics/javaagent.jar -Xms6144m -Xmx6144m -XX:MaxPermSize=1024m -Xmn2048M -XX:+UseConcMarkSweepGC -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -Dorg.apache.tomcat.util.http.ServerCookie.ALLOW_EQUALS_IN_VALUE=true -Dorg.apache.tomcat.util.http.ServerCookie.FWD_SLASH_IS_SEPARATOR=false -Dhttps.protocols=TLSv1,TLSv1.1,TLSv1.2 -Dignore.endorsed.dirs= -Dcatalina.base=/opt/apache-tomcat_1 -Dcatalina.home=/opt/apache-tomcat_1 -Djava.io.tmpdir=/opt/apache-tomcat_1/temp -Djava.security.manager -Djava.security.policy==/opt/apache-tomcat_1/conf/catalina.policy org.apache.catalina.startup.Bootstrap
5 S nobody    2655  2654 32  80   0 - 2360788 hrtime Oct16 ?      2-20:23:16 jsvc.exec -java-home /usr/lib/jvm/jre -user nobody -pidfile /opt/apache-tomcat_1/logs/catalina-daemon.pid -umask 0007 -wait 10 -errfile &1 -classpath /opt/apache-tomcat_1/bin/bootstrap.jar:/opt/apache-tomcat_1/bin/commons-daemon.jar:/opt/apache-tomcat_1/bin/tomcat-juli.jar -Djava.util.logging.config.file=/opt/apache-tomcat_1/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Dorg.apache.catalina.security.SecurityListener.UMASK=0007 -Dorg.apache.catalina.STRICT_SERVLET_COMPLIANCE=true -Dorg.apache.catalina.connector.RECYCLE_FACADES=false -Dorg.apache.catalina.connector.CoyoteAdapter.ALLOW_BACKSLASH=false -Dorg.apache.tomcat.util.buf.UDecoder.ALLOW_ENCODED_SLASH=false -Dorg.apache.coyote.USE_CUSTOM_STATUS_MSG_IN_HEADER=false -Dorg.apache.catalina.connector.Response.ENFORCE_ENCODING_IN_GET_WRITER=true -Dsun.net.inetaddr.ttl=30 -Dcom.sun.management.config.file=/opt/apache-tomcat_1/jmxconf/jvmmgmt/management.properties -javaagent:/opt/webex/webapps/appdynamics/javaagent.jar -Xms6144m -Xmx6144m -XX:MaxPermSize=1024m -Xmn2048M -XX:+UseConcMarkSweepGC -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -Dorg.apache.tomcat.util.http.ServerCookie.ALLOW_EQUALS_IN_VALUE=true -Dorg.apache.tomcat.util.http.ServerCookie.FWD_SLASH_IS_SEPARATOR=false -Dhttps.protocols=TLSv1,TLSv1.1,TLSv1.2 -Dignore.endorsed.dirs= -Dcatalina.base=/opt/apache-tomcat_1 -Dcatalina.home=/opt/apache-tomcat_1 -Djava.io.tmpdir=/opt/apache-tomcat_1/temp -Djava.security.manager -Djava.security.policy==/opt/apache-tomcat_1/conf/catalina.policy org.apache.catalina.startup.Bootstrap
 
 
6. PS 2655 Process：
[root@myservername conf]# ps -Lf 2655
UID        PID  PPID   LWP  C NLWP STIME TTY      STAT   TIME CMD
nobody    2655  2654  2655  0  147 Oct16 ?        Sl     0:02 jsvc.exec -java-home /usr/lib/jvm/jre -user nobody -pidfile /opt/apache-tomcat_1/logs/catalina-daemon.pid -umask 0007
nobody    2655  2654  2656  0  147 Oct16 ?        Sl     4:26 jsvc.exec -java-home /usr/lib/jvm/jre -user nobody -pidfile /opt/apache-tomcat_1/logs/catalina-daemon.pid -umask 0007
nobody    2655  2654  4658 13  147 Oct16 ?        Sl   1819:27 jsvc.exec -java-home /usr/lib/jvm/jre -user nobody -pidfile /opt/apache-tomcat_1/logs/catalina-daemon.pid -umask 0007
 
 
7. check java config info (is openjdk，no jre， so no jps/jstack/jmap... commands)
[root@myservername bin]# echo $JAVA_HOME
/usr/lib/jvm/jre
[root@myservername bin]# echo $PATH
/sbin:/bin:/usr/sbin:/usr/bin:/usr/lib/jvm/jre/bin
[root@myservername bin]# javac
bash: javac: command not found
[root@myservername bin]# java -version
openjdk version "1.8.0_212"
OpenJDK Runtime Environment (build 1.8.0_212-b04)
OpenJDK 64-Bit Server VM (build 25.212-b04, mixed mode)
[root@myservername bin]# 
 
 
8. The java-1.8.0-openjdk package contains just the Java Runtime Environment. If you want to develop Java programs then install the java-1.8.0-openjdk-devel package.
yum install java-1.8.0-openjdk-devel -y   (su -c "yum install java-1.8.0-openjdk")
reference: https://openjdk.java.net/install/
 
 
9. jstack -l 2655 >  2655.stack
```

因为不是该server owner，不敢去装openjdk的develop tool或者阿里的arthas等工具，所以问题hang在这里了。

https://gceasy.io/ft-index.jsp#home

