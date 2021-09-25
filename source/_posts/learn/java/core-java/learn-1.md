---
title: Java核心技术第10版原书笔记（卷一 1，2，3章）
date: 2021-07-24 14:21:23
categories:
- [学习, Java语言学习, Java核心技术卷]
tags:
- java
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Java核心技术第10版 - 卷一 - 基础知识

> 该书当前最新版为第11版
> 
> 分上下两卷（上卷为基础，下卷为高级特性）
> 
> 地址：`http://horstmann.com/corejava`

## 第1章　Java程序设计概述

1.1996年Java第一次发布就引起了人们的极大兴趣。

2.Java“白皮书”的关键术语: 简单性、面向对象、分布式、健壮性、安全性、体系结构中立、可移植性、解释型、高性能、多线程、动态性

3.上面11个术语可参考Gosling的论述：`http://horstmann.com/corejava/java-an-overview/7Gosling.pdf`

4.Java白皮书可参考：`https://www.oracle.com/java/technologies/language-environment.html`

## 第2章 Java程序设计环境

1.安装

- 你需要的是JDK（Java SE开发包），而不是JRE。
- Windows或Linux：32位选择x86，64位以x64。
- Linux：选择.tar.gz版本。

2.初学者文档：`http://docs.oracle.com/javase/tutorial/getStarted/cupojava/`

## 第3章 Java的基本程序设计结构

### 一个简单的Java应用程序

1.区分大小写、访问修饰符（访问级别）、类、类名、保留字、CamelCase、文件名与公共类名相同、扩展名为.java、main方法、代码块{}、关键字（static void）、分号结束、可多条语句写一行、双引号分隔字符串（区别于python可用单引号分隔）等。

2.根据Java语言规范，main方法必须声明为public（Java语言规范是描述Java语言的官方文档。可以从网站`http://docs.oracle.com/javase/specs`上阅读或下载）。不过，当main方法不是public时，有些版本的Java解释器也可以执行Java应用程序。有个程序员报告了这个bug。如果感兴趣的话，可以在网站`http://bugs.java.com/bugdatabase/index.jsp`上输入bug号码4252539查看。这个bug被标明“关闭，不予修复。”Sun公司的工程师解释说：Java虚拟机规范（在`http://docs.oracle.com/javase/specs/jvms/se8/html`）并没有要求main方法一定是public，并且“修复这个bug有可能带来其他的隐患”。好在，这个问题最终得到了解决。在Java SE 1.4及以后的版本中强制main方法是public的。

### 注释

单行注释<kbd>//</kbd>，多行注释<kbd>/* xxx \*/</kbd>、文档注释<kbd>/** xxx \*/</kbd>。<kbd>/**/</kbd>注释不能嵌套。

### 数据类型

Java是强类型语言，8种基本类型，4整型、2浮点、1字符、1布尔
整型类型 | 存储 | 取值范围
-- | -- | --
int | 4字节 | -21 4748 3648 ~ 21 4748 3647 （>21亿）
short | 2字节 | -32768 ~ 32767
long | 8字节 | -922 3372 0368 5477 5808 ~ 922 3372 0368 5477 5807
byte | 1字节 | -128 ~ 127
> （1）int最常用，long可表示地球人数，byte/short用于特定场合如底层文件处理等
> 
> （2）整型范围与机器无关（区别于C++）
> 
> （3）长整型后缀L，16进制前缀0x或0X，8进制前缀0（尽量少用）
> 
> （4）Java7开始二进制前缀0b或0B，且数字字面量可加下划线，易读，如1_000_000，Java编译器编译时会去除这些下划线
> 
> （5）Java 没有任何无符号(unsigned)形式的int、long、short和byte

浮点类型 | 存储 | 取值范围
-- | -- | --
float | 4字节 | -2^128 ~ +2^128
double | 8字节 | -2^1024 ~ +2^1024
> (1) double精度是float两倍，俗称双精度，大多数应用都是使用double，float无法满足
> 
> (2) float后缀为F或f，没有后缀则默认为double，浮点数后缀也可以是d或D
> 
> (3) 可以使用16进制表示浮点数，16进制表示法中p表示指数而不是e
> 
> (4) 注意运算时候溢出（正负无穷大，如一个正整数除以0）和出错（NaN，如0/0），表示为Double.POSITIVE_INFINITY、Double.NEGATIVE_INFINITY和Double.NaN
> (5) 浮点数不要用于金融计算，比如System.out.println(2.0 - 1.1)返回0.8999999999999999而不是0.9，这种舍入误差主要原因是浮点数采用二进制系统表示，而在二进制系统中无法精确地表示分数1/10，这就好像十进制无法精确地表示1/3一样，建议使用BigDecimal类。

> char类型
> 
> （1）char类型原本用于表示单个字符。现在有些变化，有些unicode字符可以用1个char描述，还有些unicode可以用2个char描述
> 
> （2）char类型用单引号括起来，使用时注意特殊字符的转义序列
> 
> （3）unicode出现之前有很多其他标准：ASCII（美国）、ISO 8859-1（西欧）、KOI-8（俄罗斯）、GB18030（中国）等
> 
> （4）1991年发布Unicode1.0 （65535，当时使用不足一半），设计Java时采用16位Unicode字符集（目前已超65536，16位char已无法满足Unicode需要了，如果想要了解Java后续怎么处理的（JavaSE5开始），可以了解下码点、代码单元、替代区域、八位数集（`https://math.ucr.edu/home/baez/octonions/`）、utf-16等概念）
> 
> （5）在Java中，char类型描述了UTF-16编码中的一个代码单元。
> 
> （6）强烈建议不要在程序中使用char类型，除非确实需要处理UTF-16代码单元。最好将字符串作为抽象数据类型处理

> boolean类型，true和false，用于逻辑判断，注意整型值和布尔值之间不能转换（区别于c++和python）

## 变量

1.注意命名规范，如果想要知道哪些Unicode字符属于Java中的“字母”，可以使用Character类的isJavaIdentifierStart和isJavaIdentifierPart方法来检查。

2.尽管$是一个合法的Java字符，但不要在你自己的代码中使用这个字符。它只用在Java编译器或其他工具生成的名字中。

3.不能使用Java保留字作为变量名（`https://www.softwaretestinghelp.com/important-java-keywords/`）

4.可以在一行中声明多个变量，如：int i, j; 不提倡。

5.声明一个变量之后，必须用赋值语句对变量进行显式初始化，千万不要使用未初始化的变量。

6.要想对一个已经声明过的变量进行赋值，就需要将变量名放在等号（=）左侧，相应取值的Java表达式放在等号的右侧。

7.在Java中，变量的声明尽可能地靠近变量第一次使用的地方，这是一种良好的程序编写风格。

8.常量，在Java中，利用关键字final指示常量。关键字final表示这个变量只能被赋值一次。一旦被赋值之后，就不能够再更改了。习惯上，常量名使用全大写。

9.经常希望某个常量可以在一个类中的多个方法中使用，通常将这些常量称为类常量。可以使用关键字static final设置一个类常量。

10.const是Java保留的关键字，但目前并没有使用。在Java中，必须使用final定义常量。

### 运算符

1.+、-、*、/，/两边都是整数则表示整数除法，否则表示浮点除，取模%。

2.注意，整数被0除将会产生一个异常，而浮点数被0除将会得到无穷大或NaN结果。

3.无论在哪个虚拟机上运行，同一运算应该得到同样的结果。对于浮点数的算术运算，实现这样的可移植性是相当困难的。double类型使用64位存储一个数值，而有些处理器使用80位浮点寄存器。这些寄存器增加了中间过程的计算精度。很多Intel处理器计算x*y，并且将结果存储在80位的寄存器中，再除以z并将结果截断为64位。这样可以得到一个更加精确的计算结果，并且还能够避免产生指数溢出。但是，这个结果可能与始终在64位机器上计算的结果不一样。因此，Java虚拟机的最初规范规定所有的中间计算都必须进行截断。这种行为遭到了数值计算团体的反对。截断计算不仅可能导致溢出，而且由于截断操作需要消耗时间，所以在计算速度上实际上要比精确计算慢。为此，Java程序设计语言承认了最优性能与理想结果之间存在的冲突，并给予了改进。在默认情况下，虚拟机设计者允许对中间计算结果采用扩展的精度。但是，对于使用strictfp关键字标记的方法必须使用严格的浮点计算来生成可再生的结果。例如，可以把main方法标记为public static strictfp void main(String[] args)，这个main方法中的所有指令都要使用严格的浮点计算（或者使用strictfp标记类则类下面的所有方法都遵循严格浮点计算）。实际的计算方式将取决于Intel处理器的行为。对大多数程序来说，浮点溢出不属于大问题。这里仅作了解。

4.数学函数与常量 - Math类（如幂运算、平方根、三角函数、指数函数和它的反函数、PI和E两个常数近似值等）。引入：import static java.lang.Math.*;

5.Math类有个floorMod方法，该方法的目的是解决一个长期存在的有关整数余数的问题。考虑表达式n%2。所有人都知道，如果n是偶数，这个表达式为0；如果n是奇数，表达式则为1。当然，除非n是负数。如果n为负，这个表达式则为-1。为什么呢？设计最早的计算机时，必须有人制定规则，明确整数除法和求余对负数操作数该如何处理。数学家们几百年来都知道这样一个最优（或“欧几里德”）规则：余数总是要≥0。不过，最早制定规则的人并没有翻开数学书好好研究，而是提出了一些看似合理但实际上很不方便的规则。 下面考虑这样一个问题：计算一个时钟时针的位置。这里要做一个时间调整，而且要归一化为一个0~11之间的数。这很简单：（position+adjustment）%12。不过，如果这个调整为负会怎么样呢？你可能会得到一个负数。所以要引入一个分支，或者使用（（position+adjustment）%12+12）%12。不管怎样，总之都很麻烦。floorMod方法就让这个问题变得容易了：floorMod（position+adjustment，12）总会得到一个0~11之间的数。（遗憾的是，对于负除数，floorMod会得到负数结果，不过这种情况在实际中很少出现。）这里注意取余和取模的差异，%为取余（rem），Math.floorMod()为取模（mod），取余运算在计算商值向0方向舍弃小数位，取模运算在计算商值向负无穷方向舍弃小数位（%在不同语言中有不同的意义，比如Java或者c/c++中%为取余，python中%则为取模）。

5.在Math类中，为了达到最快的性能，所有的方法都使用计算机浮点单元中的例程。如果得到一个完全可预测的结果比运行速度更重要的话，那么就应该使用StrictMath类。它使用“自由发布的Math库”（fdlibm）实现算法，以确保在所有平台上得到相同的结果。有关这些算法的源代码请参看`www.netlib.org/fdlibm`。从Java源码看StrictMath类主要是调用native方法实现的，也就是直接调c，而Math类很多实现又都是通过调用StrictMath类间接实现的。

6.数值之间的合法转换。有隐式转换，如下两种（通常在运算符连接交互运算时发生）：

- 丢精度，int->float, long->float, long->double
- 不丢精度，其他转换均不丢精度

7.强制类型转换。显示转换。强制类型转换通过截断小数部分将浮点值转换为整型。如果试图将一个数值从一种类型强制转换为另一种类型，而又超出了目标类型的表示范围，结果就会截断成一个完全不同的值。例如，（byte）300的实际值为44。

8.赋值，二元运算符，自增自减运算符（分后缀/前缀形式，建议少用），关系运算符（>,< ...），布尔运算符，三元操作符，位运算符（左移、右移、有符号左移、有符号右移-即最高位用符号位填充）

9.&&和||运算符是按照“短路”方式来求值的：如果第一个操作数已经能够确定表达式的值，第二个操作数就不必计算了。不过&和|运算符不采用“短路”方式来求值，也就是说，得到计算结果之前两个操作数都需要计算。

10.注意运算符优先级

11.枚举类型。

### 字符串类型

1.子串substring、拼接方式有+号拼接与join拼接

2.字符串不可变，String类没有提供修改字符串的方法且底层使用final char[]实现，不可变的优点是：编译器可以让字符串共享。总而言之，Java的设计者认为共享带来的高效率远远胜过于提取、拼接字符串所带来的低效率。查看一下程序会发现：很少需要修改字符串，而是往往需要对字符串进行比较。（区分于C++可以修改字符串中单个字符）。

3.字符串相等检测。equals（也可以使用compareTo(),不过equals看起来更清晰），一定不要用==。如果虚拟机始终将相同的字符串共享，就可以使用==运算符检测是否相等。但实际上只有字符串常量是共享的，而+或substring等操作产生的结果并不是共享的。

4.空串，即""。

5.码点与代码单元，Java字符串由char[]组成，char是一个采用utf-16编码表示unicode码点的代码单元。如length()返回采用utf-16编码表示的给定字符串所需要的代码单元数量。codePointCount()返回实际的码点数量。charAt(n)返回位置n的代码单元。

6.String API。包含50多个方法，String类实现接口有java.io.Serializable, Comparable, CharSequence。

7.构建字符串，如果需要用许多小段的字符串构建一个字符串，可以构建一个空的字符串构建器StringBuilder，可以避免耗时和浪费空间，每次添加内容时使用append(), 最后toString(), StringBuilder是jdk1.5出现的，前身为StringBuffer（jdk1.0就出现了，但是效率低，因为所有操作由synchronized修饰，所以线程安全）。

### 输入输出

1.Scanner和Console

```java
    public static strictfp void main(String[] args) {
//        Scanner in = new Scanner(System.in);
//        System.out.println("What is your name?");
//        String name = in.next();
//        System.out.println(name);

        Console console = System.console();
        String username = console.readLine("User name: ");
        char[] password = console.readPassword("Password: ");
        System.out.println(username);
        System.out.println(password);
    }
```

2.格式化输出。在早期的Java版本中，格式化数值曾引起过一些争议。庆幸的是，Java SE 5.0沿用了C语言库函数中的printf方法。具体参考printf转换符（`https://www.jquery-az.com/10-examples-learn-java-string-formatting-printf-method/`）

3.文件输入与输出。

```java
    public static strictfp void main(String[] args) throws IOException {
//        Scanner scanner = new Scanner(Paths.get("C:\\Users\\Admin\\Desktop\\test.txt"), "utf-8");
//        String text = scanner.nextLine();

//        PrintWriter pw = new PrintWriter("C:\\Users\\Admin\\Desktop\\test1.txt", "utf-8");
//        pw.write("Hello World!");
//        pw.close();

        String dir = System.getProperty("user.dir");
        System.out.println(dir);
    }
```

### 控制流程

1.Java有条件语句if-else-if、循环结构、switch多重选择语句，没有goto语句，但break语句可带标签，可以利用它实现从内层循环跳出的目的。

2.Java块作用域，方法块嵌套（不能重复声明同名变量，区别于c++，c++内块变量重定义会覆盖外部）。

3.循环有while do或do while，fori，foreach（jdk1.5出现）。

4.switch case标签可以是

- 类型为char、byte、short或int的常量表达式。
- 枚举常量。
- 从Java SE 7开始，case标签还可以是字符串字面量。

5.流程中断控制语句。尽管Java的设计者将goto作为保留字，但实际上并没有打算在语言中使用它。通常，使用goto语句被认为是一种拙劣的程序设计风格。当然，也有一些程序员认为反对goto的呼声似乎有些过分（例如，Donald Knuth就曾编著过一篇名为《Structured Programming with goto statements》的著名文章）。这篇文章说：无限制地使用goto语句确实是导致错误的根源，但在有些情况下，偶尔使用goto跳出循环还是有益处的。Java设计者同意这种看法，甚至在Java语言中增加了一条带标签的break，以此来支持这种程序设计风格。

```java
    public static strictfp void main(String[] args) {
        int m = 10;
        read_data:
        while (m > 0){
            for (int n = 0; n < 10; n++) {
                System.out.println("m=" + m + "; n=" + n);
                if (m == 5 && n ==5) break read_data;
            }
            m --;
        }
        System.out.println("Jump when m=5 and n=5.");
    }
```

注意只能跳出语句块，不能跳入语句块，还有个continue可以跳到最内曾循环首部。

### 大数值

1.主要有BigInteger、BigDecimal。注意其运算符不是基本符号+或-。

### 数组

1.数组定义写法风格，int[] a和int a[], 推荐前者。且创建一个数字数组时，所有元素都初始化为0。boolean数组的元素会初始化为false。对象数组的元素则初始化为一个特殊值null，这表示这些元素（还）未存放任何对象。

2.注意数组越界。且一旦创建，就不能再改变其大小（此时可以使用另一种数据结构ArrayList）。数组长度允许为0。

3.注意数组初始化和匿名数组。

4.数组拷贝可以实现将1个数组拷贝到1个新的数组中去，Arrays.copyOf()。

5.命令行参数。主要可以参考main方法的参数String[] args。

6.数组排序。Arrays.sort()。该方法使用了优化的快排算法。

7.数组binarySearch()。使用二分搜索算法查找。

8.多维数组。如二维数组int[][] a。java实际上没有多维数组，只有一维数组，所以其实本质上还是一维数组，或者可以理解为数组的数组。可以这样打印：Arrays.deepToString(a)。Java还可以构建不规则数组。

