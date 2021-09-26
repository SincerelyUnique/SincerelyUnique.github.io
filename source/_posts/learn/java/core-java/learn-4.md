---
title: 原书笔记（卷一 第4章）
date: 2021-07-24 23:40:28
categories:
- [学习, Java语言学习, Java核心技术卷(第10版)]
tags:
- java
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 第4章　Java程序设计概述

## 4.1 面向对象程序设计概述

1.面向对象程序设计（简称OOP）是当今主流的程序设计范型，它已经取代了20世纪70年代的“结构化”过程化程序设计开发技术。Java是完全面向对象的。

2.传统的结构化程序设计通过设计一系列的过程（即算法）来求解问题。一旦确定了这些过程，就要开始考虑存储数据的方式。这就是Pascal语言的设计者Niklaus Wirth将其著作命名为《算法+数据结构=程序》（Algorithms+Data Structures=Programs，Prentice Hall，1975）的原因。需要注意的是，在Wirth命名的书名中，算法是第一位的，数据结构是第二位的，这就明确地表述了程序员的工作方式。首先要确定如何操作数据，然后再决定如何组织数据，以便于数据操作。而OOP却调换了这个次序，将数据放在第一位，然后再考虑操作数据的算法。

3.注意面向过程和面向对象的区别。

### 4.1.1 类

1.正如前面所看到的，用Java编写的所有代码都位于某个类的内部。标准的Java库提供了几千个类，可以用于用户界面设计、日期、日历和网络程序设计。尽管如此，还是需要在Java程序中创建一些自己的类，以便描述应用程序所对应的问题域中的对象。

2.封装（encapsulation，有时称为数据隐藏）是与对象有关的一个重要概念。从形式上看，封装不过是将数据和行为组合在一个包中，并对对象的使用者隐藏了数据的实现方式。对象中的数据称为实例域（instance field），操纵数据的过程称为方法（method）。对于每个特定的类实例（对象）都有一组特定的实例域值。这些值的集合就是这个对象的当前状态（state）。无论何时，只要向对象发送一个消息，它的状态就有可能发生改变。封装给对象赋予了“黑盒”特征，这是提高重用性和可靠性的关键。

3.OOP的另一个原则会让用户自定义Java类变得轻而易举，这就是：可以通过扩展一个类来建立另外一个新的类。事实上，在Java中，所有的类都源自于一个“神通广大的超类”，它就是Object。在扩展一个已有的类时，这个扩展后的新类具有所扩展的类的全部属性和方法。在新类中，只需提供适用于这个新类的新方法和数据域就可以了。通过扩展一个类来建立另外一个类的过程称为继承（inheritance）。

### 4.1.2 对象

1.OOP中对象的三个主要特性：

- 对象的行为（behavior）——可以对对象施加哪些操作，或可以对对象施加哪些方法？
- 对象的状态（state）——当施加那些方法时，对象如何响应？
- 对象标识（identity）——如何辨别具有相同行为与状态的不同对象？

### 4.1.3 识别类

1.首先从设计类开始，然后再往每个类中添加方法。

2.识别类的简单规则是在分析问题的过程中寻找名词，而方法对应着动词。

### 4.1.4 类之间的关系

1.类之间最常见的关系有：

- 依赖（“uses-a”）
- 聚合（“has-a”）
- 继承（“is-a”）

2.依赖（dependence）：即“uses-a”关系，是一种最明显的、最常见的关系。例如，Order类使用Account类是因为Order对象需要访问Account对象查看信用状态。但是Item类不依赖于Account类，这是因为Item对象与客户账户无关。

3.解耦：应该尽可能地将相互依赖的类减至最少。如果类A不知道B的存在，它就不会关心B的任何改变（这意味着B的改变不会导致A产生任何bug）。用软件工程的术语来说，就是让类之间的耦合度最小。

4.聚合（aggregation）：即“has-a”关系，是一种具体且易于理解的关系。例如，一个Order对象包含一些Item对象。聚合关系意味着类A的对象包含类B的对象。

5.继承（inheritance）：即“is-a”关系，是一种用于表示特殊与一般关系的。例如，Rush Order类由Order类继承而来。在具有特殊性的RushOrder类中包含了一些用于优先处理的特殊方法，以及一个计算运费的不同方法；而其他的方法，如添加商品、生成账单等都是从Order类继承来的。一般而言，如果类A扩展类B，类A不但包含从类B继承的方法，还会拥有一些额外的功能。

6.UML类图，注意类之前关系以及相对应关系应当使用的UML连接符。

## 4.2 使用预定义类

1.在Java中，没有类就无法做任何事情，然而，并不是所有的类都具有面向对象特征。例如，Math类。Math类只封装了功能，它不需要也不必隐藏数据。还有Date类。

### 4.2.1 对象与对象变量

1.要想使用对象，就必须首先构造对象，并指定其初始状态。然后，对对象应用方法。

2.在Java中，使用构造器（constructor）构造新实例。构造器是一种特殊的方法，用来构造并初始化对象。构造器的名字应该与类名相同。

3.Java为什么用类描述时间而不是像其他语言那样使用一个内置的（build-in）类型？（Java的日期类库有些混乱，已经重新设计了两次）

4.一定要认识到：一个对象变量并没有实际包含一个对象，而仅仅引用一个对象。在Java中，任何对象变量的值都是对存储在另外一个地方的一个对象的引用。new操作符的返回值也是一个引用。可以显式地将对象变量设置为null，表明这个对象变量目前没有引用任何对象（在C++中没有空引用，并且引用不能被赋值。可以将Java的对象变量看作C++的对象指针。）。所有的Java对象都存储在堆中。当一个对象包含另一个对象变量时，这个变量依然包含着指向另一个堆对象的指针。

5.在Java中，必须使用clone方法获得对象的完整拷贝。

### 4.2.2 Java类库中的LocalDate类

1.Date类的实例有一个状态，即特定的时间点。尽管在使用Date类时不必知道这一点，但时间是用距离一个固定时间点的毫秒数（可正可负）表示的，这个点就是所谓的纪元（epoch），它是UTC时间1970年1月1日00：00：00。UTC是Coordinated Universal Time的缩写，与大家熟悉的GMT（即Greenwich Mean Time，格林威治时间）一样，是一种具有实践意义的科学标准时间。 但是，Date类所提供的日期处理并没有太大的用途。Java类库的设计者认为：像“December 31，1999，23：59：59”这样的日期表示法只是阳历的固有习惯。这种特定的描述法遵循了世界上大多数地区使用的Gregorian阳历表示法。但是，同一时间点采用中国的农历表示和采用希伯来的阴历表示就很不一样，对于火星历来说就更不可想象了。类库设计者决定将保存时间与给时间点命名分开。所以标准Java类库分别包含了两个类：一个是用来表示时间点的Date类；另一个是用来表示大家熟悉的日历表示法的LocalDate类。Java SE 8引入了另外一些类来处理日期和时间的不同方面。将时间与日历分开是一种很好的面向对象设计。通常，最好使用不同的类表示不同的概念。

2.LocalDate对象，可以用方法getYear、getMonthValue和getDayOfMonth得到年、月和日。实际上，Date类（from jdk1.0）还有getDay、getMonth以及getYear等方法，然而并不推荐使用这些方法（类库设计者意识到应当单独提供类来处理日历，已被标注为@Deprecated）。

### 4.2.3 更改器方法和访问器方法

1.只访问对象而不修改对象的方法有时称为访问器方法（accessor method）。例如，LocalDate.getYear和GregorianCalendar.get就是访问器方法。

2.相反，访问对象并修改对象的属性的方法称为更改器方法。

```java
    public static strictfp void main(String[] args) {
        LocalDate now1 = LocalDate.now();
        LocalDate aThousandYearLater = now1.plusDays(1000);
        System.out.println(now1.toString());  //2021-07-24
        System.out.println(aThousandYearLater.toString());  //2024-04-19

        GregorianCalendar now2 = new GregorianCalendar();
        now2.add(Calendar.DAY_OF_MONTH, 1000);
        System.out.println(now2.get(Calendar.YEAR));  // 2024
        // 下面月份返回3，日常人活中的月份都是从1开始计算的，而Calendar类从0开始算，
        // 于是乎便解释了为何取出的月份比实际少1
        // 格里高利历和罗马儒略历中一年中的第一个月是 JANUARY，它为 0；最后一个月取决于一年中的月份数。
        System.out.println(now2.get(Calendar.MONTH));  // 3
        System.out.println(now2.get(Calendar.DAY_OF_MONTH));  //19
    }
```

## 用户自定义类

### 4.3.1 Employee类

1.文件名必须与public类的名字相匹配。在一个源文件中，只能有一个公有类，但可以有任意数目的非公有类。

### 4.3.2 多个源文件的使用

1.习惯于将每一个类存在一个单独的源文件中。

2.当Java编译器发现EmployeeTest.java使用了Employee类时会查找名为Employee.class的文件。如果没有找到这个文件，就会自动地搜索Employee.java，然后，对它进行编译。更重要的是：如果Employee.java版本较已有的Employee.class文件版本新，Java编译器就会自动地重新编译这个文件。（无需显式编译Employee.java）

### 4.3.3 剖析Employee类

1.可以用public标记实例域，但这是一种极为不提倡的做法。public数据域允许程序中的任何方法对其进行读取和修改。这就完全破坏了封装。任何类的任何方法都可以修改public域，从我们的经验来看，某些代码将使用这种存取权限，而这并不我们所希望的，因此，这里强烈建议将实例域标记为private。

2.类通常包括类型属于某个类类型的实例域（实例域可以本身就是对象）。

### 4.3.4 从构造器开始

1.构造器与其他的方法有一个重要的不同。构造器总是伴随着new操作符的执行被调用，而不能对一个已经存在的对象调用构造器来达到重新设置实例域的目的。

2.特点

- 构造器与类同名
- 每个类可以有一个以上的构造器
- 构造器可以有0个、1个或多个参数
- 构造器没有返回值
- 构造器总是伴随着new操作一起调用（要记住所有的Java对象都是在堆中构造的）
- 如果一个类没有显式定义构造器，那么它会默认拥有一个无参构造器

3.请注意，不要在构造器中定义与实例域重名的局部变量。

### 4.3.5 隐式参数与显式参数

1.显式参数是明显地列在方法声明中的，隐式参数没有出现在方法声明中。（有些人把隐式参数称为方法调用的目标或接收者。有些程序员偏爱用关键字this表示隐式参数，如this.salary）

### 4.3.6 封装的优点

1.pojo的getter方法都是典型的访问器方法。由于它们只返回实例域值，因此又称为域访问器。不要将实例域声明为public，防止对实例域的破坏（破坏封装）。通常我们在设定一个pojo时需要遵循：

- 一个私有的数据域（private xxx）
- 一个公有的域访问器方法（getter）
- 一个公有的域更改器方法（setter）

2.上述的优点

- 更改内部实例不会影响外部其他代码
- 更改器方法可以执行错误检查，在实例域赋值时执行检查

3.注意不要编写返回引用可变对象的访问器方法。在Employee类中就违反了这个设计原则，其中的getHireDay方法返回了一个Date类对象。LocalDate类没有更改器方法，与之不同，Date类有一个更改器方法setTime，可以在这里设置毫秒数。 Date对象是可变的，这一点就破坏了封装性！出错的原因很微妙。d和harry.hireDay引用同一个对象。对d调用更改器方法就可以自动地改变这个雇员对象的私有状态！(如果需要返回一个可变对象的引用，应当先对他进行克隆-clone，并且实例域建议使用LocalDate)

```java
    public static strictfp void main(String[] args) {
        Employee harry = ...;
        Date d = harry.getHireDay();
        double tenSeconds = 10 * 1000;
        // 下面对d的对象的改变会影响到harry实例域，
        // 因为d和harry.getHireDay引用同一对象
        d.setTime(d.getTime() - (long) tenSeconds);
    }
```

```java
    public Date getHireDay() {
        return (Date) hireDay.clone();  // 解决方式之一，修改更改器方法
    }
```

### 4.3.7 基于类的访问权限

1.方法可以访问所调用对象的私有数据。一个方法可以访问所属类的所有对象的私有数据，比如equals方法比较两个Employee对象名字是否相同，这是合法的。

### 4.3.8 私有方法

1.在实现一个类时，由于公有数据非常危险，所以应该将所有的数据域都设置为私有的。而对于方法，尽管绝大多数方法都被设计为公有的，但在某些特殊情况下，也可能将它们设计为私有的。这可能是一个独立的辅助方法，通常，这些辅助方法不应该成为公有接口的一部分，这是由于它们往往与当前的实现机制非常紧密，或者需要一个特别的协议以及一个特别的调用次序。最好将这样的方法设计为private的。方便后续维护和删除（弃用）。

### 4.3.9 final实例域

1.可以将实例域定义为final。构建对象时必须初始化这样的域。也就是说，必须确保在每一个构造器执行之后，这个域的值被设置，并且在后面的操作中，不能够再对它进行修改。例如，可以将Employee类中的name域声明为final，因为在对象构建之后，这个值不会再被修改，即没有setName方法。

2.final修饰符大都应用于基本（primitive）类型域，或不可变（immutable）类的域（如果类中的每个方法都不会改变其对象，这种类就是不可变的类。例如，String类就是一个不可变的类）。 对于可变的类，使用final修饰符可能会对读者造成混乱。

## 4.4 静态域和静态方法

### 4.4.1 静态域

1.如果将域定义为static，每个类中只有一个这样的域。而每一个对象对于所有的实例域却都有自己的一份拷贝。在绝大多数的面向对象程序设计语言中，静态域被称为类域。

### 4.4.2 静态常量

1.静态变量使用得比较少，但静态常量却使用得比较多。例如，在Math类中定义了一个静态常量PI。如果关键字static被省略，PI就变成了Math类的一个实例域。需要通过Math类的对象访问PI，并且每一个Math对象都有它自己的一份PI拷贝。

2.由于每个类对象都可以对公有域进行修改，所以，最好不要将域设计为public。然而，公有常量（即final域）却没问题。

3.如果查看一下System类，就会发现有一个setOut方法，它可以将System.out设置为不同的流。读者可能会感到奇怪，为什么这个方法可以修改final变量的值。原因在于，setOut方法是一个本地方法，而不是用Java语言实现的。本地方法可以绕过Java语言的存取控制机制。这是一种特殊的方法，在自己编写程序时，不应该这样处理。

### 4.4.3 静态方法

1.静态方法是一种不能向对象实施操作的方法。例如，Math类的pow方法就是一个静态方法。在运算时不使用任何Math对象，换句话说就是没有隐式的参数。可以认为静态方法是没有this参数的方法（在一个非静态的方法中，this参数表示这个方法的隐式参数）。

2.但是，静态方法可以访问自身类中的静态域。（属于类且不属于类对象的变量和函数）

3.可以使用对象调用静态方法。例如，如果harry是一个Employee对象，可以用harry.getNextId（）代替Employee.getNextId（）。不过，这种方式很容易造成混淆，其原因是getNextId方法计算的结果与harry毫无关系。我们建议使用类名，而不是对象来调用静态方法。

4.下面两种情况下使用静态方法：

- 一个方法不需要访问对象状态，其所需参数都是通过显式参数提供（例如：Math.pow）。
- 一个方法只需要访问类的静态域（例如：Employee.getNextId）。

### 4.4.4 工厂方法

1.静态方法还有另外一种常见的用途。类似LocalDate和NumberFormat的类使用静态工厂方法（factory method）来构造对象。

### 4.4.5 main方法

1.main方法不对任何对象进行操作。事实上，在启动程序时还没有任何一个对象。静态的main方法将执行并创建程序所需要的对象。

2.每一个类可以有一个main方法。这是一个常用于对类进行单元测试的技巧。

## 方法参数

1.首先回顾一下在程序设计语言中有关将参数传递给方法（或函数）的一些专业术语。按值调用（call by value）表示方法接收的是调用者提供的值。而按引用调用（call by reference）表示方法接收的是调用者提供的变量地址。一个方法可以修改传递引用所对应的变量值，而不能修改传递值调用所对应的变量值。“按……调用”（call by）是一个标准的计算机科学术语，它用来描述各种程序设计语言（不只是Java）中方法参数的传递方式（事实上，以前还有按名调用（call by name），Algol程序设计语言是最古老的高级程序设计语言之一，它使用的就是这种参数传递方式。不过，对于今天，这种传递方式已经成为历史）。

2.Java程序设计语言总是采用按值调用。也就是说，方法得到的是所有参数值的一个拷贝，特别是，方法不能修改传递给它的任何参数变量的内容。

3.方法参数共有两种类型： （1）基本数据类型（数字、布尔值）。 （2）对象引用。

4.很多程序设计语言（特别是，C++和Pascal）提供了两种参数传递的方式：值调用和引用调用（如c++传参&符号）。有些程序员（甚至本书的作者）认为Java程序设计语言对对象采用的是引用调用，实际上，这种理解是不对的。（可以以实际交换两个Employee对象为例）

5.总结一下Java中方法参数的使用情况：

- 一个方法不能修改一个基本数据类型的参数（即数值型或布尔型）。
- 一个方法可以改变一个对象参数的状态。
- 一个方法不能让对象参数引用一个新的对象。

## 对象构造

### 4.6.1 重载

1.重载：如果多个方法（比如，StringBuilder构造器方法）有相同的名字、不同的参数，便产生了重载。编译器必须挑选出具体执行哪个方法，它通过用各个方法给出的参数类型与特定方法调用所使用的值类型进行匹配来挑选出相应的方法。如果编译器找不到匹配的参数，就会产生编译时错误，因为根本不存在匹配，或者没有一个比其他的更好。（这个过程被称为重载解析（overloading resolution）。）

2.Java允许重载任何方法，而不只是构造器方法。因此，要完整地描述一个方法，需要指出方法名以及参数类型。这叫做方法的签名（signature）。例如，String类有4个称为indexOf的公有方法。它们的签名是

- indexOf(int)
- indexOf(int, int)
- indexOf(String)
- indexOf(String, int)

3.返回类型不是方法签名的一部分。也就是说，不能有两个名字相同、参数类型也相同却返回不同类型值的方法。

### 4.6.2 默认域初始化

1.如果在构造器中没有显式地给域赋予初值，那么就会被自动地赋为默认值：数值为0、布尔值为false、对象引用为null。然而，只有缺少程序设计经验的人才会这样做。确实，如果不明确地对域进行初始化，就会影响程序代码的可读性。

### 4.6.3 无参数构造器

1.很多类都包含一个无参数的构造函数，对象由无参数构造函数创建时，其状态会设置为适当的默认值。

2.如果在编写一个类时没有编写构造器，那么系统就会提供一个无参数构造器。这个构造器将所有的实例域设置为默认值。于是，实例域中的数值型数据设置为0、布尔型数据设置为false、所有对象变量将设置为null。

3.如果类中提供了至少一个构造器，但是没有提供无参数的构造器，则在构造对象时如果没有提供参数就会被视为不合法。

4.请记住，仅当类没有提供任何构造器的时候，系统才会提供一个默认的构造器。如果在编写类的时候，给出了一个构造器，哪怕是很简单的，要想让这个类的用户能够无参构造实例，就必须提供一个默认的构造器（即不带参数的构造器）。

### 4.6.4 显式域初始化

1.通过重载类的构造器方法，可以采用多种形式设置类的实例域的初始状态。确保不管怎样调用构造器，每个实例域都可以被设置为一个有意义的初值，这是一种很好的设计习惯。 可以在类定义中，直接将一个值赋给任何域。例如：

2.在执行构造器之前，先执行赋值操作。当一个类的所有构造器都希望把相同的值赋予某个特定的实例域时，这种方式特别有用。 初始值不一定是常量值。在下面的例子中，可以调用方法对域进行初始化。（在C++中，不能直接初始化类的实例域。所有的域必须在构造器中设置。）

### 4.6.5 参数名

1.参数变量用同样的名字将实例域屏蔽起来。例如，如果将参数命名为salary，salary将引用这个参数，而不是实例域。但是，可以采用this.salary的形式访问实例域。回想一下，this指示隐式参数，也就是所构造的对象。

### 4.6.6 调用另一个构造器

1.关键字this引用方法的隐式参数。然而，这个关键字还有另外一个含义。 如果构造器的第一个语句形如this（...），这个构造器将调用同一个类的另一个构造器。采用这种方式使用this关键字非常有用，这样对公共的构造器代码部分只编写一次即可。

### 4.6.7 初始化块

1.初始化数据域的方法

- 在构造器中设置值
- 在声明中赋值
- 初始化块（initialization block）赋值

2.只要构造类的对象，这些块就会被执行。无论使用哪个构造器构造对象，都首先运行初始化块，然后才运行构造器的主体部分。这种机制不是必需的，也不常见。通常会直接将初始化代码放在构造器中。

3.注释： 即使在类的后面定义，仍然可以在初始化块中设置域。但是，为了避免循环定义，不要读取在后面初始化的域。具体的规则请参看Java语言规范的8.3.2.3节（   `http://docs.oracle.com/javase/specs`）。这个规则的复杂度足以使编译器的实现者头疼，因此建议将初始化块放在域定义之后。

4.由于初始化数据域有多种途径，所以列出构造过程的所有路径可能相当混乱。下面是调用构造器的具体处理步骤：

- 1）所有数据域被初始化为默认值（0、false或null）。
- 2）按照在类声明中出现的次序，依次执行所有域初始化语句和初始化块。
- 3）如果构造器第一行调用了第二个构造器，则执行第二个构造器主体。
- 4）执行这个构造器的主体。

### 4.6.8 对象析构与finalize方法

1.有些面向对象的程序设计语言，特别是C++，有显式的析构器方法，其中放置一些当对象不再使用时需要执行的清理代码。在析构器中，最常见的操作是回收分配给对象的存储空间。由于Java有自动的垃圾回收器，不需要人工回收内存，所以Java不支持析构器。当然，某些对象使用了内存之外的其他资源，例如，文件或使用了系统资源的另一个对象的句柄。在这种情况下，当资源不再需要时，将其回收和再利用将显得十分重要。 可以为任何一个类添加finalize方法。finalize方法将在垃圾回收器清除对象之前调用。在实际应用中，不要依赖于使用finalize方法回收任何短缺的资源，这是因为很难知道这个方法什么时候才能够调用。

2.有个名为System.runFinalizersOnExit（true）的方法能够确保finalizer方法在Java关闭前被调用。不过，这个方法并不安全，也不鼓励大家使用。有一种代替的方法是使用方法Runtime.addShutdownHook添加“关闭钩”（shutdown hook），如果某个资源需要在使用完毕后立刻被关闭，那么就需要由人工来管理。对象用完时，可以应用一个close方法来完成相应的清理操作。

## 4.7 包

1.使用包的主要原因是确保类名的唯一性。假如两个程序员不约而同地建立了Employee类。只要将这些类放置在不同的包中，就不会产生冲突。事实上，为了保证包名的绝对唯一性，Sun公司建议将公司的因特网域名（这显然是独一无二的）以逆序的形式作为包名，并且对于不同的项目使用不同的子包。例如，horstmann.com是本书作者之一注册的域名。逆序形式为com.horstmann。这个包还可以被进一步地划分成子包，如com.horstmann.corejava。

### 4.7.1 类的导入

1.一个类可以使用所属包中的所有类，以及其他包中的公有类（public class）。我们可以采用两种方式访问另一个包中的公有类。第一种方式是在每个类名之前添加完整的包名。还有一种是使用import。

### 4.7.2 静态导入

1.import语句不仅可以导入类，还增加了导入静态方法和静态域的功能。

### 4.7.3 将类放入包中

1.要想将一个类放入包中，就必须将包的名字放在源文件的开头，包中定义类的代码之前。

2.如果没有在源文件中放置package语句，这个源文件中的类就被放置在一个默认包（defaulf package）中。默认包是一个没有名字的包。

3.需要注意，编译器对文件（带有文件分隔符和扩展名.java的文件）进行操作。而Java解释器加载类（带有.分隔符）。

### 4.7.4 包作用域

1.标记为public的部分可以被任意的类使用；标记为private的部分只能被定义它们的类使用。如果没有指定public或private，这个部分（类、方法或变量）可以被同一个包中的所有方法访问。对于类来说，这种默认是合乎情理的。但是，对于变量来说就有些不适宜了，因此变量必须显式地标记为private，不然的话将默认为包可见。显然，这样做会破坏封装性。问题主要出于人们经常忘记键入关键字private。在java.awt包中的Window类就是一个典型的示例（反例，至今未更正）。

2.从1.2版开始，JDK的实现者修改了类加载器，明确地禁止加载用户自定义的、包名以“java.”开始的类！当然，用户自定义的类无法从这种保护中受益。然而，可以通过包密封（package sealing）机制来解决将各种包混杂在一起的问题。如果将一个包密封起来，就不能再向这个包添加类了。（如制作包含密封包的JAR文件）。

### 4.8 类路径

1.在前面已经看到，类存储在文件系统的子目录中。类的路径必须与包名匹配。

2.类文件也可以存储在JAR（Java归档）文件中。在一个JAR文件中，可以包含多个压缩形式的类文件和子目录，这样既可以节省又可以改善性能。在程序中用到第三方（third-party）的库文件时，通常会给出一个或多个需要包含的JAR文件。JDK也提供了许多的JAR文件，例如，在jre/lib/rt.jar中包含数千个类库文件。

3.提示： JAR文件使用ZIP格式组织文件和子目录。可以使用所有ZIP实用程序查看内部的rt.jar以及其他的JAR文件。

4.由于运行时库文件（rt.jar和在jre/lib与jre/lib/ext目录下的一些其他的JAR文件）会被自动地搜索，所以不必将它们显式地列在类路径中。

5.javac编译器总是在当前的目录中查找文件，但Java虚拟机仅在类路径中有“.”目录的时候才查看当前目录。如果没有设置类路径，那也并不会产生什么问题，默认的类路径包含“.”目录。然而如果设置了类路径却忘记了包含“.”目录，则程序仍然可以通过编译，但不能运行。

6.编译器定位文件要比虚拟机复杂得多。如果引用了一个类，而没有指出这个类所在的包，那么编译器将首先查找包含这个类的包，并询查所有的import指令，确定其中是否包含了被引用的类。

7.编译器的任务不止这些，它还要查看源文件（Source files）是否比类文件新。如果是这样的话，那么源文件就会被自动地重新编译。在前面已经知道，仅可以导入其他包中的公有类。一个源文件只能包含一个公有类，并且文件名必须与公有类匹配。因此，编译器很容易定位公有类所在的源文件。当然，也可以从当前包中导入非公有类。这些类有可能定义在与类名不同的源文件中。如果从当前包中导入一个类，编译器就要搜索当前包中的所有源文件，以便确定哪个源文件定义了这个类。

### 4.8.1 设置类路径

1.最好采用-classpath（或-cp）选项指定类路径。

## 4.9 文档注释

1.JDK包含一个很有用的工具，叫做javadoc，它可以由源文件生成一个HTML文档。

### 4.9.1 注释的插入

1.javadoc实用程序（utility）从下面几个特性中抽取信息：

- 包
- 共有类和接口
- 公有的和受保护的构造器及方法 
- 公有的和受保护的域

2.如果文档中有到其他文件的链接，例如，图像文件（用户界面的组件的图表或图像等），就应该将这些文件放到子目录doc-files中。javadoc实用程序将从源目录拷贝这些目录及其中的文件到文档目录中。

### 4.9.2 类注释

### 4.9.3 方法注释

- @param
- @return
- @throws

### 4.9.4 域注释

只需要对公有域（通常指的是静态常量）建立文档。

### 4.9.5 通用注释

- @author
- @version
- @since
- @deprecated
- @see
  
### 4.9.6 包与概述注释

要想产生包注释，就需要在每一个包目录中添加一个单独的文件。

- 1）提供一个以package.html命名的HTML文件。在标记```<body>...</body>```之间的所有文本都会被抽取出来。
- 1）2）提供一个以package-info.java命名的Java文件。这个文件必须包含一个初始的以```/**```和```*/```界定的Javadoc注释，跟随在一个包语句之后。它不应该包含更多的代码或注释。

### 4.9.7 注释的抽取

javadoc -d docDirectory1 docDirectory2 ...

## 4.10 类设计技巧（OOP）

- 1.一定要保证数据私有
- 2.一定要对数据初始化
- 3.不要在类中使用过多的基本类型
- 4.不是所有的域都需要独立的域访问器和域更改器
- 5.将职责过多的类进行分解
- 6.类名和方法名要能够体现他的职责
- 7.优先使用不可变类
