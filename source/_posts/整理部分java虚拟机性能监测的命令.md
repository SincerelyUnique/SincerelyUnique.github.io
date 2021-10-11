---
title: 整理部分java虚拟机性能监测的命令
date: 2017-12-04 12:20:05
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

在《深入理解java虚拟机》学习的第四章，Java虚拟机自身为我们提供了多种虚拟机性能监控与故障处理工具，这里做一下记录，方便以后查阅。

参考官方文档：[JDK Tools and Utilities](https://docs.oracle.com/javase/1.5.0/docs/tooldocs/)

# 如何查看一个java虚拟机进程，可以使用ps查，如下

```bash
[root@yjyapp ~]# ps -elf|grep java
0 S root      5816     1  0  80   0 - 2071759 futex_ Nov09 ?      04:29:00 /home/jdk1.7/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/app/tomcat_gps/apache-tomcat-7.0.79/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/app/tomcat_gps/apache-tomcat-7.0.79/endorsed -classpath /app/tomcat_gps/apache-tomcat-7.0.79/bin/bootstrap.jar:/app/tomcat_gps/apache-tomcat-7.0.79/bin/tomcat-juli.jar -Dcatalina.base=/app/tomcat_gps/apache-tomcat-7.0.79 -Dcatalina.home=/app/tomcat_gps/apache-tomcat-7.0.79 -Djava.io.tmpdir=/app/tomcat_gps/apache-tomcat-7.0.79/temp org.apache.catalina.startup.Bootstrap start
0 S root     21712 19844  0  80   0 - 25816 pipe_w 10:19 pts/0    00:00:00 grep java
0 S root     28431     1  0  80   0 - 1771037 futex_ Nov10 ?      01:58:39 /home/jdk1.7/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -server -Xms2048M -Xmx2048M -XX:PermSize=512M -XX:MaxPermSize=512M -Xss256k -XX:MaxTenuringThreshold=20 -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:CMSFullGCsBeforeCompaction=2 -XX:SoftRefLRUPolicyMSPerMB=0 -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/endorsed -classpath /usr/local/tomcat-performance-test/apache-tomcat-7.0.79/bin/bootstrap.jar:/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79 -Dcatalina.home=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79 -Djava.io.tmpdir=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/temp org.apache.catalina.startup.Bootstrap start
[root@yjyapp ~]# 
```

或者使用jps查寻

```bash
[root@yjyapp ~]# jps -v
22161 Jps -Denv.class.path=.:/home/jdk1.7/jdk1.7.0_80/lib/dt.jar:/home/jdk1.7/jdk1.7.0_80/lib/tools.jar -Dapplication.home=/home/jdk1.7/jdk1.7.0_80 -Xms8m
28431 Bootstrap -Djava.util.logging.config.file=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xms2048M -Xmx2048M -XX:PermSize=512M -XX:MaxPermSize=512M -Xss256k -XX:MaxTenuringThreshold=20 -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:CMSFullGCsBeforeCompaction=2 -XX:SoftRefLRUPolicyMSPerMB=0 -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/endorsed -Dcatalina.base=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79 -Dcatalina.home=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79 -Djava.io.tmpdir=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/temp
5816 Bootstrap -Djava.util.logging.config.file=/app/tomcat_gps/apache-tomcat-7.0.79/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/app/tomcat_gps/apache-tomcat-7.0.79/endorsed -Dcatalina.base=/app/tomcat_gps/apache-tomcat-7.0.79 -Dcatalina.home=/app/tomcat_gps/apache-tomcat-7.0.79 -Djava.io.tmpdir=/app/tomcat_gps/apache-tomcat-7.0.79/temp
[root@yjyapp ~]# 
```

由于jps并不像ps使用应用程序名来查询进程，所以它查找所有的Java应用程序，包括即使没有使用java执行体的那种（注：jps仅查找当前用户的Java进程）

下面我们针对某个进程来使用jdk监测工具

# jps虚拟机进程状况工具

参考：[官方文档](https://docs.oracle.com/javase/1.5.0/docs/tooldocs/share/jps.html)

## jps -m

输出虚拟机进程启动时传递给主类main（）函数的参数

```bash
[root@yjyapp ~]# jps -m
22544 Jps -m
28431 Bootstrap start
5816 Bootstrap start
[root@yjyapp ~]# 
```

## jps -l

输出主类的全名，如果进程执行的是jar包，输出jar路径

```bash
[root@yjyapp ~]# jps -l
23002 sun.tools.jps.Jps
28431 org.apache.catalina.startup.Bootstrap
5816 org.apache.catalina.startup.Bootstrap
[root@yjyapp ~]# 
```

## jps -v

输出虚拟机进程启动时jvm参数

```bash
[root@yjyapp ~]# jps -v
23094 Jps -Denv.class.path=.:/home/jdk1.7/jdk1.7.0_80/lib/dt.jar:/home/jdk1.7/jdk1.7.0_80/lib/tools.jar -Dapplication.home=/home/jdk1.7/jdk1.7.0_80 -Xms8m
28431 Bootstrap -Djava.util.logging.config.file=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xms2048M -Xmx2048M -XX:PermSize=512M -XX:MaxPermSize=512M -Xss256k -XX:MaxTenuringThreshold=20 -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:CMSFullGCsBeforeCompaction=2 -XX:SoftRefLRUPolicyMSPerMB=0 -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/endorsed -Dcatalina.base=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79 -Dcatalina.home=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79 -Djava.io.tmpdir=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/temp
5816 Bootstrap -Djava.util.logging.config.file=/app/tomcat_gps/apache-tomcat-7.0.79/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/app/tomcat_gps/apache-tomcat-7.0.79/endorsed -Dcatalina.base=/app/tomcat_gps/apache-tomcat-7.0.79 -Dcatalina.home=/app/tomcat_gps/apache-tomcat-7.0.79 -Djava.io.tmpdir=/app/tomcat_gps/apache-tomcat-7.0.79/temp
[root@yjyapp ~]# 
```

## jps -q

只输出LVMID，省略主类的名称

```bash
[root@yjyapp ~]# jps -q
23157
28431
5816
[root@yjyapp ~]# 
```

## jps -V

（注意这里是大写的V）输出LVMID，及主类的名称

```bash
[root@yjyapp ~]# jps -V
23276 Jps
28431 Bootstrap
5816 Bootstrap
[root@yjyapp ~]# 
```

## jps

可以直接输入jps，效果同（5）

```bash
[root@yjyapp ~]# jps
23561 Jps
28431 Bootstrap
5816 Bootstrap
[root@yjyapp ~]# 
```

# jstat虚拟机统计信息监视工具

参考：[官方文档](https://docs.oracle.com/javase/1.5.0/docs/tooldocs/share/jstat.html)

## jstat -class pid

显示加载class的数量，及所占空间等信息。（pid以28431为例）

```bash
[root@yjyapp ~]# jstat -class 28431
Loaded  Bytes  Unloaded  Bytes     Time   
  7928 16097.5        0     0.0       8.23
[root@yjyapp ~]# 
```

## jstat -compiler pid

显示VM实时编译的数量等信息。 

```bash
[root@yjyapp ~]# jstat -compiler 28431
Compiled Failed Invalid   Time   FailedType FailedMethod
    2121      1       0    30.13          1 java/lang/ClassLoader getClassLoadingLock
[root@yjyapp ~]# 
```

## jstat -gc pid

可以显示gc的信息，查看gc的次数，及时间。其中最后五项，分别是young gc的次数，young gc的时间，full gc的次数，full gc的时间，gc的总时间。

```bash
[root@yjyapp ~]# jstat -gc 28431
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       PC     PU    YGC     YGCT    FGC    FGCT     GCT   
68096.0 68096.0  0.0   7433.1 545344.0 232442.6 1415616.0   94136.2   524288.0 48490.8     57    3.055   0      0.000    3.055
[root@yjyapp ~]# 
```

## jstat -gccapacity pid

可以显示，VM内存中三代（young,old,perm）对象的使用和占用大小，如：PGCMN显示的是最小perm的内存使用量，PGCMX显示的是perm的内存最大使用量，PGC是当前新生成的perm内存占用量，PC是但前perm内存占用量。其他的可以根据这个类推， OC是old内纯的占用量。 

```bash
[root@yjyapp ~]# jstat -gccapacity 28431
 NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC      PGCMN    PGCMX     PGC       PC     YGC    FGC 
681536.0 681536.0 681536.0 68096.0 68096.0 545344.0  1415616.0  1415616.0  1415616.0  1415616.0 524288.0 524288.0 524288.0 524288.0     57     0
[root@yjyapp ~]# 
```

## jstat -gccause pid

统计gc信息统计。

```bash
[root@yjyapp ~]# jstat -gccause 28431
  S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT    LGCC                 GCC                 
  0.00  10.92  42.62   6.65   9.25     57    3.055     0    0.000    3.055 Allocation Failure   No GC               
[root@yjyapp ~]# 
```

## jstat -gcnew pid

new对象的信息。 

```bash
[root@yjyapp ~]# jstat -gcnew 28431
 S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT  
68096.0 68096.0    0.0 7433.1 16  20 34048.0 545344.0 232442.6     57    3.055
[root@yjyapp ~]# 
```

## jstat -gcnewcapacity pid

new对象的信息及其占用量。 

```bash
[root@yjyapp ~]# jstat -gcnewcapacity 28431
  NGCMN      NGCMX       NGC      S0CMX     S0C     S1CMX     S1C       ECMX        EC      YGC   FGC 
  681536.0   681536.0   681536.0  68096.0  68096.0  68096.0  68096.0   545344.0   545344.0    57     0
[root@yjyapp ~]# 
```

## jstat -gcold pid

老年代信息统计

```bash
[root@yjyapp ~]# jstat -gcold 28431
   PC       PU        OC          OU       YGC    FGC    FGCT     GCT   
524288.0  48490.8   1415616.0     94136.2     57     0    0.000    3.055
[root@yjyapp ~]# 
```

## jstat -gcoldcapacity pid

老年代占用等信息统计

```bash
[root@yjyapp ~]# jstat -gcoldcapacity 28431
   OGCMN       OGCMX        OGC         OC       YGC   FGC    FGCT     GCT   
  1415616.0   1415616.0   1415616.0   1415616.0    57     0    0.000    3.055
[root@yjyapp ~]# 
```

## jstat -gcpermcapacity pid

perm对象的信息及其占用量。 

```bash
[root@yjyapp ~]# jstat -gcpermcapacity 28431
  PGCMN      PGCMX       PGC         PC      YGC   FGC    FGCT     GCT   
  524288.0   524288.0   524288.0   524288.0    57     0    0.000    3.055
[root@yjyapp ~]# 
```

## jstat -gcutil pid

同（5）

```bash
[root@yjyapp ~]# jstat -gcutil 28431
  S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT   
  0.00  10.92  42.62   6.65   9.25     57    3.055     0    0.000    3.055
[root@yjyapp ~]# 
```

## jstat -printcompilation pid

当前VM执行的信息。 

```bash
[root@yjyapp ~]# jstat -printcompilation 28431
Compiled  Size  Type Method
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
[root@yjyapp ~]# 
```

说明：上面的命令还可以在结尾添加两个参数，如下：表示每250毫秒打印一次，一共打印6次

```bash
[root@yjyapp ~]# jstat -printcompilation 28431 250 6
Compiled  Size  Type Method
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
[root@yjyapp ~]# 
```

上面的命令还可以加一个参数-h3，意思是每3行打印一下标题，如下

```bash
[root@yjyapp ~]# jstat -printcompilation -h3 28431 250 6
Compiled  Size  Type Method
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
Compiled  Size  Type Method
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
    2121   1050    1 sun/net/spi/DefaultProxySelector$2 run
[root@yjyapp ~]# 
```

# jinfo配置信息工具

参考：[官方文档](https://docs.oracle.com/javase/1.5.0/docs/tooldocs/share/jinfo.html)

## jinfo -flag MaxPermSize pid

Java配置信息。配置信息包括Java系统属性和JVM命令行标志。能输出并修改运行时的java进程的运行参数

```bash
[root@yjyapp ~]#  jinfo -flag MaxPermSize 28431
-XX:MaxPermSize=536870912
[root@yjyapp ~]# 
```

# jmap内存映像工具

参考：[官方文档](https://docs.oracle.com/javase/1.5.0/docs/tooldocs/share/jmap.html)
参考：[cn blog](https://www.cnblogs.com/itdev/p/6103310.html)

## jmap -histo pid

打印堆的直方图。对于每个Java类，将打印对象数量，以字节为单位的内存大小以及完全限定的类名称。 VM内部类名称以“*”前缀打印。

```bash
[root@yjyapp ~]# jmap -histo 28431
... ...
3479:             1             16  org.apache.xpath.objects.XMLStringFactoryImpl
3480:             1             16  org.apache.ibatis.ognl.ObjectNullHandler
3481:             1             16  sun.text.normalizer.NormalizerBase$NFCMode
3482:             1             16  java.util.regex.Pattern$CharPropertyNames$19
3483:             1             16  org.apache.log4j.helpers.AppenderAttachableImpl
3484:             1             16  com.google.gson.internal.bind.TypeAdapters$1
Total       4460009      417507696
```

## jmap -heap pid

打印堆汇总信息

```bash
[root@yjyapp ~]# jmap -heap 28431
Attaching to process ID 28431, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 24.80-b11

using parallel threads in the new generation.
using thread-local object allocation.
Concurrent Mark-Sweep GC

Heap Configuration:
   MinHeapFreeRatio = 40
   MaxHeapFreeRatio = 70
   MaxHeapSize      = 2147483648 (2048.0MB)
   NewSize          = 697892864 (665.5625MB)
   MaxNewSize       = 697892864 (665.5625MB)
   OldSize          = 1395851264 (1331.1875MB)
   NewRatio         = 2
   SurvivorRatio    = 8
   PermSize         = 536870912 (512.0MB)
   MaxPermSize      = 536870912 (512.0MB)
   G1HeapRegionSize = 0 (0.0MB)

Heap Usage:
New Generation (Eden + 1 Survivor Space):
   capacity = 628162560 (599.0625MB)
   used     = 281853872 (268.7967987060547MB)
   free     = 346308688 (330.2657012939453MB)
   44.86957516220005% used
Eden Space:
   capacity = 558432256 (532.5625MB)
   used     = 274242344 (261.53787994384766MB)
   free     = 284189912 (271.02462005615234MB)
   49.10933081917102% used
From Space:
   capacity = 69730304 (66.5MB)
   used     = 7611528 (7.258918762207031MB)
   free     = 62118776 (59.24108123779297MB)
   10.915667311589521% used
To Space:
   capacity = 69730304 (66.5MB)
   used     = 0 (0.0MB)
   free     = 69730304 (66.5MB)
   0.0% used
concurrent mark-sweep generation:
   capacity = 1449590784 (1382.4375MB)
   used     = 96395424 (91.92984008789062MB)
   free     = 1353195360 (1290.5076599121094MB)
   6.649836979096026% used
Perm Generation:
   capacity = 536870912 (512.0MB)
   used     = 49654592 (47.35430908203125MB)
   free     = 487216320 (464.64569091796875MB)
   9.248888492584229% used

26066 interned Strings occupying 2995456 bytes.
[root@yjyapp ~]# 
```

## jmap -permstat pid

打印Java堆永久生成的类加载器智能统计信息。对于每个类加载器，都会打印其名称，活性，地址，父类加载器以及它所加载的类的数量和大小。

```bash
[root@yjyapp ~]# jmap -permstat 28431
Attaching to process ID 28431, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 24.80-b11
finding class loader instances ..
...  ...
```

## jmap -dump:format=b,file=fileName pid

fileName为dump出的文件名字，你自己定义，如下面的String文件

```bash
[root@yjyapp jalen]# jmap -dump:format=b,file=String 28431
Dumping heap to /home/jalen/String ...
Heap dump file created
[root@yjyapp jalen]# ls
apache-tomcat-7.0.81.tar.gz  catalina.jar  iftop-0.17.tar.gz  META-INF  org  String
```

# jconsoleGUI程序

参考：

[官方文档](https://docs.oracle.com/javase/1.5.0/docs/guide/management/jconsole.html) 

[官方文档2](https://docs.oracle.com/javase/1.5.0/docs/tooldocs/share/jconsole.html)

## jconsole pid

jconsole是一个用java写的GUI程序，用来监控VM，并可监控远程的VM

# jstack堆栈跟踪工具

参考：[官方文档](https://docs.oracle.com/javase/1.5.0/docs/tooldocs/share/jstack.html)

# jhat虚拟机堆转储快照分析工具

参考：未找到，似乎被移除了

# jsadebugd调试工具

# 附录：
 
1. 如果我们想要查服务器都启动了哪些tomcat，可以使用如下ps查寻，如下，共启动了两个tomcat进程

```bash
[root@yjyapp ~]# ps -elf|grep tomcat
0 S root      5816     1  0  80   0 - 2071759 futex_ Nov09 ?      04:29:02 /home/jdk1.7/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/app/tomcat_gps/apache-tomcat-7.0.79/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/app/tomcat_gps/apache-tomcat-7.0.79/endorsed -classpath /app/tomcat_gps/apache-tomcat-7.0.79/bin/bootstrap.jar:/app/tomcat_gps/apache-tomcat-7.0.79/bin/tomcat-juli.jar -Dcatalina.base=/app/tomcat_gps/apache-tomcat-7.0.79 -Dcatalina.home=/app/tomcat_gps/apache-tomcat-7.0.79 -Djava.io.tmpdir=/app/tomcat_gps/apache-tomcat-7.0.79/temp org.apache.catalina.startup.Bootstrap start
0 S root     21915 19844  0  80   0 - 25816 pipe_w 10:22 pts/0    00:00:00 grep tomcat
0 S root     28431     1  0  80   0 - 1771037 futex_ Nov10 ?      01:58:40 /home/jdk1.7/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -server -Xms2048M -Xmx2048M -XX:PermSize=512M -XX:MaxPermSize=512M -Xss256k -XX:MaxTenuringThreshold=20 -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:CMSFullGCsBeforeCompaction=2 -XX:SoftRefLRUPolicyMSPerMB=0 -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/endorsed -classpath /usr/local/tomcat-performance-test/apache-tomcat-7.0.79/bin/bootstrap.jar:/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79 -Dcatalina.home=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79 -Djava.io.tmpdir=/usr/local/tomcat-performance-test/apache-tomcat-7.0.79/temp org.apache.catalina.startup.Bootstrap start
[root@yjyapp ~]# 
```

2. 如果我们想要查询端口为8080的tomcat的pid，可以使用netstat查询，如下可以看到其进程为5816

```bash
[root@yjyapp ~]# netstat -ntulp|grep 8080
tcp        0      0 :::8080                     :::*                        LISTEN      5816/java 
```
