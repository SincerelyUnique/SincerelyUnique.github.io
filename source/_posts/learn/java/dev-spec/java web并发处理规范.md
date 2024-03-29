---
title: java web并发处理规范
date: 2017-09-14 08:36:05
tags:
- principle
categories:
- [学习, Java语言学习, Java开发规范]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 【强制】获取单例对象要线程安全。在单例对象里面做操作也要保证线程安全。
   说明： 资源驱动类、工具类、单例工厂类都需要注意。

2. 【强制】线程资源必须通过线程池提供，不允许在应用中自行显式创建线程。
   说明：使用线程池的好处是减少在创建和销毁线程上所花的时间以及系统资源的开销，解决资
   源不足的问题。如果不使用线程池，有可能造成系统创建大量同类线程而导致消耗完内存或者
   “ 过度切换” 的问题。
   （多线程，使用线程池，就像数据库有数据库连接池，http有http连接池）

3. 【强制】 SimpleDateFormat 是线程不安全的类，一般不要定义为 static 变量，如果定义为
   static，必须加锁，或者使用 DateUtils 工具类。
   正例： 注意线程安全，使用 DateUtils。亦推荐如下处理：
```java
private static final ThreadLocal<DateFormat> df = new ThreadLocal<DateFormat>() {
    @Override
    protected DateFormat initialValue() {
        return new SimpleDateFormat("yyyy-MM-dd");
    }
};
```
说明： 如果是 JDK8 的应用，可以使用 instant 代替 Date， Localdatetime 代替 Calendar，
Datetimeformatter 代替 Simpledateformatter，官方给出的解释： simple beautiful strong
immutable thread-safe。
（放心使用java8）

4. 【强制】高并发时，同步调用应该去考量锁的性能损耗。能用无锁数据结构，就不要用锁；能
   锁区块，就不要锁整个方法体；能用对象锁，就不要用类锁。

5. 【强制】对多个资源、数据库表、对象同时加锁时，需要保持一致的加锁顺序，否则可能会造
   成死锁。
   说明： 线程一需要对表 A、 B、 C 依次全部加锁后才可以进行更新操作，那么线程二的加锁顺序
   也必须是 A、 B、 C，否则可能出现死锁。

6. 【强制】并发修改同一记录时，避免更新丢失，要么在应用层加锁，要么在缓存加锁，要么在
   数据库层使用乐观锁，使用 version 作为更新依据。
   说明： 如果每次访问冲突概率小于 20%，推荐使用乐观锁，否则使用悲观锁。乐观锁的重试次
   数不得小于 3 次。
   
   解释锁：
   - 悲观锁(Pessimistic Lock), 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。
   - 乐观锁(Optimistic Lock), 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库如果提供类似于write_condition机制的其实都是提供的乐观锁。

7. 【强制】多线程并行处理定时任务时， Timer 运行多个 TimeTask 时，只要其中之一没有捕获
   抛出的异常，其它任务便会自动终止运行，使用 ScheduledExecutorService 则没有这个问题。

8. 【强制】线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式，这样
   的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。
   
   说明： Executors 各个方法的弊端：
   - 1） newFixedThreadPool 和 newSingleThreadExecutor:
   主要问题是堆积的请求处理队列可能会耗费非常大的内存，甚至 OOM。
   - 2） newCachedThreadPool 和 newScheduledThreadPool:
   主要问题是线程数最大数是 Integer.MAX_VALUE，可能会创建数量非常多的线程，甚至 OOM。

9. 【强制】创建线程或线程池时请指定有意义的线程名称，方便出错时回溯。
   正例（专门跑定时任务的线程）：
```java
public class TimerTaskThread extends Thread {
public TimerTaskThread(){
    super.setName("TimerTaskThread"); …
}
```

10. 【推荐】使用 CountDownLatch 进行异步转同步操作，每个线程退出前必须调用 countDown 方法，线程执行代码注意 catch 异常，确保 countDown 方法可以执行，避免主线程无法执行至countDown 方法，直到超时才返回结果。
说明： 注意，子线程抛出异常堆栈，不能在主线程 try-catch 到。

11. 【推荐】避免 Random 实例被多线程使用，虽然共享该实例是线程安全的，但会因竞争同一 seed导致的性能下降。
   说明： Random 实例包括 java.util.Random 的实例或者 Math.random()实例。
   正例： 在 JDK7 之后，可以直接使用 API ThreadLocalRandom，在 JDK7 之前，可以做到每个线
   程一个实例。

12. 【推荐】通过双重检查锁（ double-checked locking）（在并发场景）实现延迟初始化的优化
   问题隐患(可参考 The "Double-Checked Locking is Broken" Declaration),推荐问题解决方
   案中较为简单一种（适用于 jdk5 及以上版本），将目标属性声明为 volatile 型（比如反例
   中修改 helper 的属性声明为 private volatile Helper helper = null;）；
   反例：
   ```java
   class Foo {
       private Helper helper = null;
       public Helper getHelper() {
           if (helper == null) synchronized(this) {
               if (helper == null) helper = new Helper();
           }
           return helper; 
       }
       // other functions and members...
   }
   ```

13. 【参考】 volatile 解决多线程内存不可见问题。对于一写多读，是可以解决变量同步问题，
   但是如果多写，同样无法解决线程安全问题。如果想取回 count++数据，使用如下类实现：
   `AtomicInteger count = new AtomicInteger(); count.addAndGet(1); `
    `count++`操作如果是
   JDK8，推荐使用 LongAdder 对象，比 AtomicLong 性能更好（减少乐观锁的重试次数）。

14. 【参考】注意 HashMap 的扩容死链，导致 CPU 飙升的问题。

15. 【参考】ThreadLocal 无法解决共享对象的更新问题，ThreadLocal 对象建议使用 static 修饰。
   这个变量是针对一个线程内所有操作共有的，所以设置为静态变量，所有此类实例共享此静态
   变量 ，也就是说在类第一次被使用时装载，只分配一块存储空间，所有此类的对象(只要是这
   个线程内定义的)都可以操控这个变量


