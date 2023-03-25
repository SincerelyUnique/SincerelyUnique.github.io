---
title: 简单处理Java内存泄漏与OOM
date: 2023-03-24 16:35:40
tags: oom
categories:
- [学习, Java语言学习, Java]
---

> 背景：前端访问某个接口报500，Cannot allocate new IntPointer(1): totalBytes = 0, physicalBytes = 7775M，但是访问其他的一些接口正常。

## 查看服务器日志

服务器上有很多内存泄漏的`警告`日志和`严重`日志，很多。

```bash
[root@localhost ~]# docker logs -f 1d75d7d2abcb | grep Exception
```

```raw
23-Mar-2023 13:12:50.636 警告 [Catalina-utility-1] org.apache.catalina.loader.WebappClassLoaderBase.clearReferencesThreads Web应用程序[xxx]似乎启动了一个名为[client_][generic][T#3]]的线程，但未能停止它。这很可能会造成内存泄漏。线程的堆栈跟踪：[
 sun.misc.Unsafe.park(Native Method)
 java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
 java.util.concurrent.LinkedTransferQueue.awaitMatch(LinkedTransferQueue.java:737)
 java.util.concurrent.LinkedTransferQueue.xfer(LinkedTransferQueue.java:647)
 java.util.concurrent.LinkedTransferQueue.take(LinkedTransferQueue.java:1269)
 java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
 java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
 java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
 java.lang.Thread.run(Thread.java:748)]
23-Mar-2023 13:12:50.640 警告 [Catalina-utility-1] org.apache.catalina.loader.WebappClassLoaderBase.clearReferencesThreads Web应用程序[xxx]似乎启动了一个名为[[_client_][generic][T#4]]的线程，但未能停止它。这很可能会造成内存泄漏。线程的堆栈跟踪：[
 sun.misc.Unsafe.park(Native Method)
 java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
 java.util.concurrent.LinkedTransferQueue.awaitMatch(LinkedTransferQueue.java:737)
 java.util.concurrent.LinkedTransferQueue.xfer(LinkedTransferQueue.java:647)
 java.util.concurrent.LinkedTransferQueue.take(LinkedTransferQueue.java:1269)
 java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1074)
 java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1134)
 java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
 java.lang.Thread.run(Thread.java:748)]

23-Mar-2023 13:12:50.650 严重 [Catalina-utility-1] org.apache.catalina.loader.WebappClassLoaderBase.checkThreadLocalMapForLeaks web应用程序[xxx]创建了一个Thre型为[java.lang.ThreadLocal]（值为[java.lang.ThreadLocal@36b9c3a3]），值类型为[io.netty.util.internal.InternalThreadLocalMap]（值为[io.netty.util.internal.InternalThreadLocalMap@web应用程序时未能将其删除。线程将随着时间的推移而更新，以尝试避免可能的内存泄漏
23-Mar-2023 13:12:50.650 严重 [Catalina-utility-1] org.apache.catalina.loader.WebappClassLoaderBase.checkThreadLocalMapForLeaks web应用程序[xxx]创建了一个Threocal，其键类型为[java.lang.ThreadLocal]（值为[java.lang.ThreadLocal@36b9c3a3]），值类型为[io.netty.util.internal.InternalThreadLocalMap]（值为[io.netty.util.internal.InternalThr03），但在停止web应用程序时未能将其删除。线程将随着时间的推移而更新，以尝试避免可能的内存泄漏
23-Mar-2023 13:12:50.651 严重 [Catalina-utility-1] org.apache.catalina.loader.WebappClassLoaderBase.checkThreadLocalMapForLeaks web应用程序[xxx]创建了一个Thre型为[java.lang.ThreadLocal]（值为[java.lang.ThreadLocal@36b9c3a3]），值类型为[io.netty.util.internal.InternalThreadLocalMap]（值为[io.netty.util.internal.InternalThreadLocalMap@web应用程序时未能将其删除。线程将随着时间的推移而更新，以尝试避免可能的内存泄漏
```



## 查看Linux服务器内存使用

```bash
# 检查下内存
[root@localhost ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:            31G         15G        7.4G         35M        8.0G         14G
Swap:          2.0G          0B        2.0G
```

```bash
# top看下实时内存使用情况，可以看到total=32778416，free=7819664，可见vm内存够用，同时能够在COMMAND列锁定该java进程pid为30164
[root@localhost ~]# top
top - 09:15:10 up 21 days, 22:59,  1 user,  load average: 0.16, 0.23, 0.21
Tasks: 303 total,   1 running, 302 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.5 us,  3.4 sy,  0.0 ni, 96.1 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem : 32778416 total,  7825416 free, 16587464 used,  8365536 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used. 15711900 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                                      
 8984 root      20   0  162232   2340   1528 R  22.6  0.0   0:00.23 top                                                                                                          
  991 root      20   0 1044128  12672   7592 S   3.2  0.0   7:57.36 NetworkManager                                                                                               
30164 root      20   0   15.4g   7.6g  18000 S   3.2 24.3 540:05.34 java                                                                                                         
    1 root      20   0  194156   7272   4172 S   0.0  0.0   3:44.03 systemd                                                                                                      
    2 root      20   0       0      0      0 S   0.0  0.0   0:03.97 kthreadd     
```


```bash
# 查看进程下线程状况，可以看到该java进程（或者说jvm进程）下有363个线程，每个线程使用内存24.3
[root@localhost ~]# top -Hp 30164
top - 09:14:36 up 21 days, 22:58,  1 user,  load average: 0.16, 0.24, 0.21
Threads: 363 total,   0 running, 363 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.4 us,  0.4 sy,  0.0 ni, 99.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem : 32778416 total,  7824408 free, 16588480 used,  8365528 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used. 15710884 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                                                                                                       
30276 root      20   0   15.4g   7.6g  18000 S  0.3 24.3  17:44.52 java                                                                                                          
30387 root      20   0   15.4g   7.6g  18000 S  0.3 24.3   1:52.16 java                                                                                                          
30389 root      20   0   15.4g   7.6g  18000 S  0.3 24.3   1:51.18 java                                                                                                          
  527 root      20   0   15.4g   7.6g  18000 S  0.3 24.3   2:00.88 java                                                                                                          
 3481 root      20   0   15.4g   7.6g  18000 S  0.3 24.3   0:54.77 java                                                                                                          
 3485 root      20   0   15.4g   7.6g  18000 S  0.3 24.3   2:01.18 java                                                                                                          
 3498 root      20   0   15.4g   7.6g  18000 S  0.3 24.3   0:48.24 java                                                                                                          
 4706 root      20   0   15.4g   7.6g  18000 S  0.3 24.3   5:01.12 java      
```

```bash
# 也可查看当前进程下有多少线程
[root@localhost ~]# cat /proc/30164/status | grep Threads
Threads:        363
```

```raw
# 查看启动的容器，根据接口和名称锁定容器id，如果无法锁定是哪个容器，inspect下宿主机进程号，如果是30164，就对上了, xxx可以百度下，这里和markdown语法冲突，没有列出来
[root@localhost ~]# docker ps
[root@localhost ~]# docker inspect -f 'xxx' 1d75d7d2abcb
```

```bash
# 查看该容器挂载点，通常会将容器内的一些目录进行挂载，方便我们直接在宿主机查看
[root@localhost ~]# docker inspect 1d75d7d2abcb|grep "Mount" -A 30
"Destination": "/opt/tomcat/tomcat8/webapps"
```

```bash
# 进入容器
[root@localhost ~]# docker exec -it 1d75d7d2abcb /bin/bash
```

```bash
# 尝试看下容器内的jdk，还好是jdk，这样就可以使用jps，jsat，jmap一些命令了
root@1d75d7d2abcb:/# java -version
java version "1.8.0_202"
Java(TM) SE Runtime Environment (build 1.8.0_202-b08)
Java HotSpot(TM) 64-Bit Server VM (build 25.202-b08, mixed mode)
```

```bash
# jps看下java进程，定位到容器内进程pid=1
root@1d75d7d2abcb:/# jps
1 Bootstrap
7959 Jps
```

```bash
# jstat看下该进程堆详细状况，可以看到新生代满了
# NGCMN：新生代最小容量
# NGCMX：新生代最大容量  1397760
# NGC：当前新生代容量    1397760
# S0C：第一个幸存区大小    25088
# S1C：第二个幸存区的大小  23552
# EC：伊甸园区的大小      1349120
# OGCMN：老年代最小容量
# OGCMX：老年代最大容量   2796544
# OGC：当前老年代大小     2375680
# OC:当前老年代大小
# MCMN:最小元数据容量
# MCMX：最大元数据容量
# MC：当前元数据空间大小
# CCSMN：最小压缩类空间大小
# CCSMX：最大压缩类空间大小
# CCSC：当前压缩类空间大小
# YGC：年轻代gc次数   1624
# FGC：老年代gc次数   820
# 这里抛出一个问题：为什么新生代使用100%，老年代还有剩余，为何新生代对象不转移到老年代？按理说对象应该会直接进入老年代
# 这里可以看到新生代和老年代还是遵循1:2的比例，也就是虚拟机参数都是默认的
# -XX:NewRatio=2   ：新生代和年老代的堆内存占用比例, 例如2表示新生代占年老代的1/2，占整个堆内存的1/3
# -XX:SurvivorRatio=8： Eden与Survivor的占用比例。例如8表示，一个survivor区占用 1/8 的Eden内存，即1/10的新生代内存
root@1d75d7d2abcb:/# jstat -gccapacity 1
 NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC       MCMN     MCMX      MC     CCSMN    CCSMX     CCSC    YGC    FGC 
174592.0 1397760.0 1397760.0 25088.0 23552.0 1349120.0   349696.0  2796544.0  2375680.0  2375680.0      0.0 1751040.0 812632.0      0.0 1048576.0 110208.0   1624   820
```

```bash
# 知道进程pid了，根据pid确定进程名，其实已经可以肯定是tomcat容器进程了，下面查询都指向了tomcat
root@1d75d7d2abcb:~# top
root@1d75d7d2abcb:~# ps -elf|grep java
root@1d75d7d2abcb:~# ps -elf|grep tomcat
root@1d75d7d2abcb:~# ps -ef|grep 1
```

```bash
# 使用jmap dump堆快照，看服务启动时间，我们这边服务启动了几天，生成了1.6G的快照
root@1d75d7d2abcb:~# jmap -dump:file=dump.hprof 1

# 把dump的文件移动到挂载点拷贝出来，或者使用docker cp命令拷贝出来，拿到本地

# 如果上面的快照不大，可以尝试使用这个网站分析：https://gceasy.io/ft-index.jsp

# 其实最好还是自己下载一个JProfiler，安装下，然后将dump文件导入进去分析。JProfiler也可以集成在Intelligent Idea里，查看动态运行的程序内存快照
# 下载JProfiler，https://pan.baidu.com/s/1EJxkS2U3cmF8JHQlJJYILA
# 打开JProfiler，heap walker，打开dump的快照
# 通过分析快照，发现java程序中使用的ES客户端工具频繁的做垃圾回收，回收过程中产生了很多大对象，并不是我们手写的程序本身造成的
# 我们使用的是5.x的ES，版本很老了，而且由于短期无法升级ES客户端，所以选择提升tomcat容器运存（总感觉不太好）


# 查看tomcat运存，当前为4G，调整后重启docker容器
root@1d75d7d2abcb:/usr/local/tomcat# cat bin/catalina.sh |grep "JAVA_OPT"
JAVA_OPTS="-Xms512m -Xmx4096m -XX:MaxPermSize=256m"
```

## 其他猜测

用户调用某个接口出现了内存溢出问题，但是调用其他接口正常，从某方面讲，可能这个接口也是有些问题的，我们从本地调整程序运行的堆参数，分个100M堆，新生代大致33M，S0和S1各3.3M，即指定-Xmx100m,-Xms100m，测试程序该接口，发现报OOM错，该接口返回数据量不大，但是执行过程中调用了native方法（我们有引入动态dll库，底层是大量的运算），而native方法是运行在虚拟机中，需要申请额外的堆内内存，而新生代又无法接受这么多内存的分配，所以OOM，所以这也是一个原因

```raw
java.lang.OutOfMemoryError: Physical memory usage is too high: physicalBytes (196M) > maxPhysicalBytes (188M)
	at org.bytedeco.javacpp.Pointer.deallocator(Pointer.java:700)
	at org.bytedeco.javacpp.Pointer.init(Pointer.java:126)
	at org.bytedeco.javacpp.IntPointer.allocateArray(Native Method)
	at org.bytedeco.javacpp.IntPointer.<init>(IntPointer.java:90)
	这里的栈信息就不显示了~
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:190)
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:138)
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:106)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:888)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:793)
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1040)
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:943)
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006)
	at org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:909)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:660)
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:741)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at com.github.xiaoymin.swaggerbootstrapui.filter.SecurityBasicAuthFilter.doFilter(SecurityBasicAuthFilter.java:84)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at com.github.xiaoymin.swaggerbootstrapui.filter.ProductionSecurityFilter.doFilter(ProductionSecurityFilter.java:53)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:202)
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96)
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:526)
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:139)
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92)
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:74)
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:343)
	at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:408)
	at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
	at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:861)
	at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1579)
	at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
	at java.lang.Thread.run(Thread.java:750)
```


## 办法

调整tomcat运存
