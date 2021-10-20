---
title: (1) 走进java
date: 2017-08-06 11:54:36
categories:
- [学习, Java语言学习, 深入理解Java虚拟机]
tags:
- java
- jvm
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 第一章：走进Java

## java能够得到广泛认可的原因（优点）
① 摆脱硬件平台束缚，“一次编写，到处运行”；

② 提供了一个相对安全的内存管理和访问机制，避免了绝大部分的内存泄漏和指针越界问题；

③ 实现了热点代码检测和运行时编译及优化，使得java应用能够随着时间增加而获得更高的性能；

④ 有一套完善的应用程序接口，还有无数商业机构和开源社区和第三方类库来帮助实现各种功能；


## java技术体系包含以下几点
① java程序设计语言（广义上讲，Clojure，JRuby，Groovy等运行于java虚拟机上的语言及其相关的程序都属于java技术体系中的一员）；

② 各种硬件平台上的java虚拟机；

③ Class文件格式；

④ Java API类库；

⑤ 来自商业机构和开源社区的第三方java类库；


## JDK和JRE

① 我们可以把java程序设计语言，java虚拟机，java API类库这三部分统称为JDK，JDK是用于支持java程序开发的最小环境；

② 可以把java API类库中的java SE API子集和java虚拟机这两部分统称为JRE，JRE是支持java程序运行的标准环境；


## 按照java技术关注的重点业务领域来划分，java技术体系可以分为4个平台
① java Card：支持一些java小程序（Applets）运行在小内存设备（如智能卡）上的平台；

② java ME：适用移动终端平台，对java API有所精简，并加入针对移动终端的支持，以前叫j2me；

③ java SE：支持桌面级应用的java平台，以前叫j2se；

④ java EE：支持使用多层架构的企业应用（如ERP，CRM应用）的java平台，除了java se api外还有大量的扩充，以前叫j2ee；


## java发展历程
① 1995-5-23，Oak to Java；

② 1996-1-23，Java1.0；

③ 1997-2-18，Java1.1；

④ 1998-12-4，J2SE1.2，“playground”；

⑤ 2000-5-8，J2SE1.3，“Kestrel”；

⑥ 2002-2-13，J2SE1.4，“Merlin”；

⑦ 2004-9-29，JavaSE5，“tiger”；

⑧ 2006-12-11，JavaSE6，“Mustang”；

⑨ 2011-7-28，JavaSE7，“Dolphin”；

⑩ 2014-3-8，JavaSE8；


## JDK1.1版本代表技术
① Jar文件格式；

② JDBC；

③ Java Beans；

④ RMI（java远程方法调用）


## JDK1.2特点
① 技术体系拆分：J2SE，J2EE，J2ME；

② 新技术：EJB，Java Plug-in（插件），Java IDL（接口定义语言），Swing；

③ 虚拟机第一次内置JIT编译器（1.2曾并存三个虚拟机，Classic VM（只能以外挂形式使用，不含JIT），HotSpot VM-这之后各个java版本使用的默认虚拟机，Exact VM（Solaris-UNIX系统之一平台出现过））；

④ API级别：strictfp关键字，Collections集合类；


## JDK1.3特点
① 类库：添加数学运算和新的Timer API类库；

② 平台级服务提供：JNDI（之前JNDI只是一个扩展）；

③ RMI：使用CORBA IIOP实现；

④ Java 2D API；

⑤ Java Sound类库；


## JDK1.4特点（同年.net发布）
① 正则表达式，异常链，NIO，日志类，XML解析器，XSLT转换器等


## JDK1.5特点
① 自动装箱，泛型，动态注解，美剧，可变长参数，遍历循环（foreach循环）等

② 改进内存模型（Java Memory Model，JMM）；

③ 提供并发包，java.util.concurrent；


## JDK1.6特点（同年决定将java开源，OpenJDK出现，这之后便出现Sun收购案，08经济危机等等）
① 命名方式：启用Java SE 6，Java EE 6，Java Me 6（之前类似J2SE）；

② 提供动态语言支持（通过内置Mozilla Javascript Rhino引擎实现）；

③ 提供编译API和微型HTTP服务器API等；

④ 对虚拟机内部改进：锁与同步，垃圾回收，类加载等方面算法；


## JDK1.7特点（原计划）
① Lambda项目（未实现，Lambda表达式，函数式编程）；

② Jigsaw项目（未实现，Oracle收购后准备在1.8版本实现，但后来又宣布无法完成，推迟到1.9版本实现）；

③ 动态语言支持；

④ GarbageFirst收集器；

⑤ Coin项目（语言细节进化）

⑥ 提供新的G1收集器（12年update 4中才转成正式），加强对java语言的调用支持（Jsr-292，至今没有完全实现定型），升级类加载架构等


## JDK1.8特点
① 加入闭包（Lambda Expressions）；

② Lambda表达式，函数式编程；

③ 接口扩展；


## Sun Classic/Exact VM
① 世界第一款商用Java虚拟机，96年JDK1.0版本使用，纯解释器方式来执行java代码，如使用JIT需要使用sun提供的外挂编译器，已被取代；1.3版本备用，1.4版本彻底退出历史舞台；


## Sun HotSpot VM
① 目前使用范围最广，收购的（收购公司Longview）；

② 一开始就是准确式GC；

③ 热点探测，通过执行计数器找出最具有编译价值的代码，然后通知jit编译器一方法为单位进行编译，如果方法中有效循环次数很多，将会分别出发标准编译和OSR（栈上替换）编译动作；

④ 08年oracle收购BEA公司和Sun公司，这样同时拥有两款优秀的java虚拟机：JRockit和HotSpot，并宣布在不久的将来进行整合，整合方式大概是在HotSpot基础上，移植JRockit优秀的特性（如垃圾回收与MissionControl服务）


## Sun Mobile-Embedded VM/Meta-Circular VM
① KVM：android，ios出现前手机平台使用；

② CDC/CLDC HotSpot Implementation：希望在手机，电子书，pda等设备上建立统一的Java编程接口，但不乐观；

③ Squawk VM：运行于Sun SPOT（一种手持的wifi设备），也曾运用于java Card；

④ JavaInJava，实验性质的虚拟机；

⑤ Maxine VM：同JavaInJava性质；


## BEA JRockit
① 号称“世界上速度最快的java虚拟机”，不太关注程序启动速度，因此不包含解析器实现，全部代码都靠编译器编译后执行；

② 垃圾收集器和MissionControl服务套件世界领先；


## IBM J9 VM
① 由IBM Ottawa实验室一个名为SmallTalk的虚拟机发展而来；

② 主要市场：和IBM产品（如IBM WebSphere等）搭配以及在IBM AIX和z/OS这些平台部署java应用；


## Azul VM和BEA Liquid VM
① 特定硬件平台的专有虚拟机；

② 前者运行于Vega系统，后者即现在的JRockit VE，运行在自家Hypervisor系统上；


## Apache Harmony/Google Android Dalvik VM
① 只能称虚拟机，而不是java虚拟机

② Harmony没有通过TCK认证（如果一个公司要宣布自己的运行平台“兼容于java语言”，那就必须通过TCK兼容性测试，Apache基金会曾一直要求Sun公司提供TCK的使用权限，但一直遭到拒绝，知道被oracle收购后，关系越来越僵，导致Apache愤然退出JCP组织，这是java社区最严重的一次“分裂”，后来OpenJdk发布，其优势被削弱）

③ Dalvik并不是一个java虚拟机，没有遵循java虚拟机规范，不能直接执行java的class文件，使用的是寄存器架构而不是jvm中常见的栈架构，但又与java有关系，他执行的dex文件是由class文件转化而来，使用java语法编写应用程序，可以使用大部分java API；


## Microsoft JVM
① 微软为了在IE3支持java Applets应用而开发的java虚拟机，只有windows平台；

② Sun控告微软侵权，赔偿10亿美金，然而当windows xp sp3中将java虚拟机抹去时，Sun又希望微软不要这么做（很有讽刺性），这使得微软对java技术的热情消失，转为.net；


## java模块化-OSGI技术
① java1.6，1.7提出的模块化规范都没有通过，相对而言其主导权在IBM公司；

② 但其宣布将在OpenJdk中建立一个名为Jigsaw（拼图）的子项目来推动这个规范在java平台转为具体的实现（好吧，这个不得不等到java9，一直处于劣势地位）；


## 混合语言
① 背景：java平台上多语言混合编程正成为主流，每个层使用不同语言

② 朝多语言虚拟机转变


## 多核并行
① 1.5的concurrent并发包是一个粗粒度的并发框架，而1.7的java.util.concurrent.forkjoin包则是一个重要扩充；

② Fork/Join模式是处理并行编程的一个经典方法；

③ java8中Lambda支持，添加函数式编程，函数式编程一个重要优点就是这样的程序天然地适合并行运行；

④ 目前显卡的算术运算能力，并行能力已经远远超过CPU，在图形领域发掘显卡潜力是近些年计算机发展方向之一，如C语言的CUDA，Java则是OpenJDK中的子项目Sumatra项目（提供了使用GPU和APU运算能力的工具）；

⑤ JDK外围，如Apache的Hadoop Map/Reduce这些为了满足并行计算需求的并行框架，能狗运行在上千个商用服务器组成的大型集群上，并且能以一种可靠的容错方式并行处理TB级别的数据集；

⑥ 另外也出现了诸如Scala，Clojure及Erlang等天生就具备并行计算能力的语言；


## 进一步丰富语法
① java5：自动装箱，泛型，动态注解，枚举，可变长参数，遍历循环等语法

② java7进一步扩充（有些推到java8）

③ OpenJDK中建立的Coin子项目：统一处理java语法的细节修改，如二进制原生支持，在switch语法中使用字符串，“<>”操作符，异常处理的改进，简化变长参数方法的调用，面向资源的try-catch-finally语句等；

④ 除了Coin项目外，在JSR-335中定义的Lambda表达式也是；


## 64位虚拟机
① java运行于64位虚拟机需要额外代价：内存消耗过多（由于指针膨胀和各种数据类型对其补白的原因），通常比32位系统额外增加10%-30%内存消耗

② 运行速度几乎去那面落后于32位虚拟机，15%左右性能差距；

③ java ee方面企业经常需要超过4G的内存，对64位虚拟机需求迫切，但由于上述原因，很多都选择使用虚拟机集群等方式继续在32位虚拟机中进行部署；

③ JDK1.6提供普通对象指针压缩功能（-XX：+UseCompressedOops），但是维持指针压缩会增加执行代码行数，很多方面都会受到影响；


## 自己编译JDK
这个感觉比较复杂，要深入了解JDK源码，暂且略过吧

