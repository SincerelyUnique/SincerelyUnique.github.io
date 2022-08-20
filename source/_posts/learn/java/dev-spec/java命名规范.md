---
title: java命名规范
date: 2017-09-14 08:36:05
tags:
- principle
categories:
- [学习, Java语言学习, Java开发规范]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

本文转自“V型知识库”（http://www.vxzsk.com/519.html）（http://www.vxzsk.com/677.html）

1. 【强制】所有编程相关命名均不能以下划线或美元符号开始，也不能以下划线或美元符号结束。
   反例： _name / __name / $Object / name_ / name$ / Object$
   （没干过这事）

2. 【强制】所有编程相关的命名严禁使用拼音与英文混合的方式，更不允许直接使用中文的方式。
   说明： 正确的英文拼写和语法可以让阅读者易于理解，避免歧义。注意， 即使纯拼音命名方式
   也要避免采用。
   反例： DaZhePromotion [打折] / getPingfenByName() [评分] / int 变量 = 3;
   正例： ali / alibaba / taobao / cainiao / aliyun / youku / hangzhou 等国际通用的
   名称，可视为英文。
   （刚开始写程序时使用过拼音，现在不用了）

3. 【强制】类名使用 UpperCamelCase 风格，必须遵从驼峰形式，但以下情形例外：（领域模型
   的相关命名） DO / DTO / VO / DAO 等。
   正例： MarcoPolo / UserDO / XmlService / TcpUdpDeal / TaPromotion
   反例： macroPolo / UserDo / XMLService / TCPUDPDeal / TAPromotion
   （领域模型有时就写成了驼峰形式，坑）

4. 【强制】常量命名全部大写，单词间用下划线隔开，力求语义表达完整清楚，不要嫌名字长。
   正例： MAX_STOCK_COUNT
   反例： MAX_COUNT
   （这个通常常量类里都这样写的）

5. 【强制】抽象类命名使用 Abstract 或 Base 开头；异常类命名使用 Exception 结尾；测试类命
   名以它要测试的类的名称开始，以 Test 结尾。
   （这个遇到过，像BaseController等类，AbstractXXX，XXXException）

7. 【强制】中括号是数组类型的一部分，数组定义如下： String[] args;
   反例： 请勿使用 String args[]的方式来定义
   （这个刚学java时写错过）

8. 【强制】 POJO 类中的任何布尔类型的变量，都不要加 is，否则部分框架解析会引起序列化错
   误。
   反例： 定义为基本数据类型 boolean isSuccess；的属性，它的方法也是 isSuccess()， RPC
   框架在反向解析的时候， “ 以为” 对应的属性名称是 success，导致属性获取不到，进而抛出
   异常。
   （这个好像经常犯错啊，很多时候POJO没有过序列化，然后就按中文意思来了“是或者不是”，要改）

9. 【强制】包名统一使用小写，点分隔符之间有且仅有一个自然语义的英语单词。包名统一使用
   单数形式，但是类名如果有复数含义，类名可以使用复数形式。
   正例： 应用工具类包名为 com.alibaba.mpp.util、类名为 MessageUtils （此规则参考 spring
   的框架结构）
   （这个遇到过，经常有看到将util包写成utils包的，包名要用单数，而工具类里不止一个工具，却用了单数）

10. 【强制】杜绝完全不规范的缩写， 避免望文不知义。
反例： <某业务代码>AbstractClass“ 缩写” 命名成 AbsClass； condition“ 缩写” 命名成
condi，此类随意缩写严重降低了代码的可阅读性。
（这个也经常遇到过，每当写完程序，跑下checkstyle就会看到好多）

11. 【推荐】如果使用到了设计模式，建议在类名中体现出具体模式。
说明： 将设计模式体现在名字中，有利于阅读者快速理解架构设计思想。
正例： public class OrderFactory;
public class LoginProxy;
public class ResourceObserver;
（这个没用过，对四人组的模式还是不太熟，只接触过几个，谈不上用）

12. 【推荐】接口类中的方法和属性不要加任何修饰符号（ public 也不要加），保持代码的简洁
性，并加上有效的 javadoc 注释。尽量不要在接口里定义变量，如果一定要定义变量，肯定是
与接口方法相关，并且是整个应用的基础常量。
正例： 接口方法签名： void f();
接口基础常量表示： String COMPANY = "alibaba";
反例： 接口方法定义： public abstract void f();
说明： JDK8 中接口允许有默认实现，那么这个 default 方法，是对所有实现类都有价值的默
认实现。 （原来java8的default是例外的）

13. 接口和实现类的命名有两套规则：
- 1）【强制】对于 Service 和 DAO 类，基于 SOA 的理念，暴露出来的服务一定是接口，内部
的实现类用 Impl 的后缀与接口区别。
正例： CacheServiceImpl 实现 CacheService 接口
- 2）【推荐】 如果是形容能力的接口名称，取对应的形容词做接口名（通常是–able 的形
式）。
正例： AbstractTranslator 实现 Translatable。
（这个刚开始编程时，好像直接暴露过实现类。。。，形容能力的able接口没有定义过，不过公司现在统一将接口以“I”开头）

14. 【参考】枚举类名建议带上 Enum 后缀，枚举成员名称需要全大写，单词间用下划线隔开。
说明： 枚举其实就是特殊的常量类，且构造方法被默认强制是私有。
正例： 枚举名字： DealStatusEnum；成员名称： SUCCESS / UNKOWN_REASON。
（倒没怎么用过枚举）

15. 【参考】各层命名规约：
- A) Service/DAO 层方法命名规约
   - 1） 获取单个对象的方法用 get 做前缀。
   - 2） 获取多个对象的方法用 list 做前缀。
   - 3） 获取统计值的方法用 count 做前缀。
   - 4） 插入的方法用 save（推荐）或 insert 做前缀。
   - 5） 删除的方法用 remove（推荐）或 delete 做前缀。
   - 6） 修改的方法用 update 做前缀。
- B) 领域模型命名规约
   - 1） 数据对象： xxxDO， xxx 即为数据表名。
   - 2） 数据传输对象： xxxDTO， xxx 为业务领域相关的名称。
   - 3） 展示对象： xxxVO， xxx 一般为网页名称。
   - 4） POJO 是 DO/DTO/BO/VO 的统称，禁止命名成 xxxPOJO

（这个有很多习惯性的就用上了）


