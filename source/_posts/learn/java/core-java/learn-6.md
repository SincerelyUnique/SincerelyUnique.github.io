---
title: Java核心技术第10版原书笔记（卷一 第6章）
date: 2021-07-26 13:33:55
categories:
- [学习, Java语言学习, Java核心技术卷]
tags:
- java
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 第6章 接口、lambda表达式与内部类

> 接口 - 主要用来描述类具有什么功能，而并不给出每个功能的具体实现。
>
> 内部类
>
> 接口示例
>
> 代理
>
> lambda表达式

## 6.1 接口

### 6.1.1 接口概念

1.接口不是类，而是对类的一组需求描述。

2.“如果类遵从某个特定接口，那么就履行这项服务”。

3.接口中的所有方法自动地属于public。因此，在接口中声明方法时，不必提供关键字public。

4.接口绝对不能含有实例域。

5.如果希望使用Arrays类的sort方法对Employee对象数组进行排序，Employee类就必须实现Comparable接口。

6.compareTo():在调用x.compareTo（y）的时候，这个compareTo方法必须确实比较两个对象的内容，并返回比较的结果。当x小于y时，返回一个负数；当x等于y时，返回0；否则返回一个正数。

7.为了让类实现一个接口，通常需要下面两个步骤:

- 1）将类声明为实现给定的接口。使用关键字implements。
- 2）对接口中的所有方法进行定义。

8.在接口声明中，没有将compareTo方法声明为public，这是因为在接口中的所有方法都自动地是public。不过，在实现接口时，必须把方法声明为public；否则，编译器将认为这个方法的访问属性是包可见性，即类的默认访问属性，之后编译器就会给出试图提供更严格的访问权限的警告信息。

9.不符合“反对称”的规则。如果x是一个Employee对象，y是一个Manager对象，调用x.compareTo（y）不会抛出异常，它只是将x和y都作为雇员进行比较。但是反过来，y.compareTo（x）将会抛出一个ClassCastException。

### 6.1.2 接口的特性

1.不能使用new实例化一个接口，即不能构造接口的对象，但是能声明接口的变量。

2.可以以接口为基础扩展一个接口。

3.虽然在接口中不能包含实例域或静态方法，但却可以包含常量。与接口中的方法都自动地被设置为public一样，接口中的域将被自动设为public static final。

4.接口可以只定义常量，如SwingConstants接口。不过这样使用有些偏离接口的初衷。

5.Cloneable接口。

### 6.1.3 接口与抽象类

1.使用抽象类表示通用属性存在这样一个问题：每个类只能扩展于一个类。假设Employee类已经扩展于一个类，例如Person，它就不能再像下面这样扩展第二个类了。

2.有些程序设计语言允许一个类有多个超类，例如C++。我们将此特性称为多重继承（multiple inheritance）。而Java的设计者选择了不支持多继承，其主要原因是多继承会让语言本身变得非常复杂（如同C++），效率也会降低（如同Eiffel）。实际上，接口可以提供多重继承的大多数好处，同时还能避免多重继承的复杂性和低效性。

### 6.1.4 静态方法

在Java SE 8中，允许在接口中增加静态方法。理论上讲，没有任何理由认为这是不合法的。只是这有违于将接口作为抽象规范的初衷。

### 6.1.5 默认方法

1.可以为接口方法提供一个默认实现。必须用default修饰符标记这样一个方法。

2.在Java SE 8中，可以把所有方法声明为默认方法，这些默认方法什么也不做。

3.在Java API中，你会看到很多接口都有相应的伴随类，这个伴随类中实现了相应接口的部分或所有方法，如Collection/AbstractCollection或MouseListener/MouseAdapter。在Java SE 8中，这个技术已经过时。现在可以直接在接口中实现方法。

### 6.1.6 解决默认方法冲突

1.如果先在一个接口中将一个方法定义为默认方法，然后又在超类或另一个接口中定义了同样的方法，会发生什么情况？（类优先规则）

- 1）超类优先。如果超类提供了一个具体方法，同名而且有相同参数类型的默认方法会被忽略。
- 2）接口冲突。如果一个超接口提供了一个默认方法，另一个接口提供了一个同名而且参数类型（不论是否是默认参数）相同的方法，必须覆盖这个方法来解决冲突。

2.“类优先”规则可以确保与Java SE 7的兼容性。如果为一个接口增加默认方法，这对于有这个默认方法之前能正常工作的代码不会有任何影响。

3.千万不要让一个默认方法重新定义Object类中的某个方法。例如，不能为toString或equals定义默认方法，尽管对于List之类的接口这可能很有吸引力。由于“类优先”规则，这样的方法绝对无法超越Object.toString或Objects.equals。

## 6.2 接口示例

### 6.2.1 接口与回调

回调（callback）是一种常见的程序设计模式。在这种模式中，可以指出某个特定事件发生时应该采取的动作。例如，可以指出在按下鼠标或选择某个菜单项时应该采取什么行动。

### 6.2.2 Comparator接口

### 6.2.3 对象克隆

1.对象克隆没有这么简单。clone方法是Object的一个protected方法，这说明你的代码不能直接调用这个方法。只有Employee类可以克隆Employee对象。这个限制是有原因的。想想看Object类如何实现clone。它对于这个对象一无所知，所以只能逐个域地进行拷贝。如果对象中的所有数据域都是数值或其他基本类型，拷贝这些域没有任何问题。但是如果对象包含子对象的引用，拷贝域就会得到相同子对象的另一个引用，这样一来，原对象和克隆的对象仍然会共享一些信息。

2.默认的克隆操作是“浅拷贝”，并没有克隆对象中引用的其他对象。使用时可能带来安全问题。

3.浅拷贝安全性：如果原对象和浅克隆对象共享的子对象是不可变的，那么这种共享就是安全的。如果子对象属于一个不可变的类，如String，就是这种情况。或者在对象的生命期中，子对象一直包含不变的常量，没有更改器方法会改变它，也没有方法会生成它的引用，这种情况下同样是安全的。

4.在这里，Cloneable接口的出现与接口的正常使用并没有关系。具体来说，它没有指定clone方法，这个方法是从Object类继承的。这个接口只是作为一个标记，指示类设计者了解克隆过程。对象对于克隆很“偏执”，如果一个对象请求克隆，但没有实现这个接口，就会生成一个受查异常。

5.Cloneable接口是Java提供的一组标记接口（tagging interface）之一。不包含任何方法。它唯一的作用就是允许在类型查询中使用instanceof。建议在自己的程序中不要使用标记接口。

6.建立深拷贝，还需要做更多工作，即克隆对象中可变的实例域。

7.克隆没有你想象中那么常用。标准库中只有不到5%的类实现了clone。

8.所有数组类型都有一个public的clone方法，而不是protected。可以用这个方法建立一个新数组，包含原数组所有元素的副本。

## 6.3 lambda表达式

### 6.3.1 为什么引入lambda表达式

lambda表达式是一个可传递的代码块，可以在以后执行一次或多次。

### 6.3.2 lambda表达式的语法

### 6.3.3 函数式接口

1.对于只有一个抽象方法的接口，需要这种接口的对象时，就可以提供一个lambda表达式。这种接口称为函数式接口（functional interface）。

2.实际上，在Java中，对lambda表达式所能做的也只是能转换为函数式接口。在其他支持函数字面量的程序设计语言中，可以声明函数类型（如（String，String）->int）、声明这些类型的变量，还可以使用变量保存函数表达式。不过，Java设计者还是决定保持我们熟悉的接口概念，没有为Java语言增加函数类型。

3.`Predicate<T>`接口。

### 6.3.4 方法引用

1.用：：操作符分隔方法名与对象或类名。主要有3种情况：

- object：：instanceMethod
- Class：：staticMethod
- Class：：instanceMethod

2.在前2种情况中，方法引用等价于提供方法参数的lambda表达式。即System.out：：println等价于x->System.out.println（x）。类似地，Math：：pow等价于（x，y）->Math.pow（x，y）。对于第3种情况，第1个参数会成为方法的目标。例如，String：：compareToIgnoreCase等同于（x，y）->x.compareToIgnoreCase（y）。

3.如果有多个同名的重载方法，编译器就会尝试从上下文中找出你指的那一个方法。例如，Math.max方法有两个版本，一个用于整数，另一个用于double值。选择哪一个版本取决于Math：：max转换为哪个函数式接口的方法参数。类似于lambda表达式，方法引用不能独立存在，总是会转换为函数式接口的实例。

### 6.3.5 构造器引用

构造器引用与方法引用很类似，只不过方法名为new。例如，Person：：new是Person构造器的一个引用。哪一个构造器呢？这取决于上下文。

### 6.3.6 变量作用域

1.lambda表达式有3个部分：

- 1）一个代码块；
- 2）参数；
- 3）自由变量的值，这是指非参数而且不在代码中定义的变量。

2.关于代码块以及自由变量值有一个术语：闭包（closure）。在Java中，lambda表达式就是闭包。

3.这里有一条规则：lambda表达式中捕获的变量必须实际上是最终变量（effectively final）。实际上的最终变量是指，这个变量初始化之后就不会再为它赋新值。

4.lambda表达式的体与嵌套块有相同的作用域。这里同样适用命名冲突和遮蔽的有关规则。在lambda表达式中声明与一个局部变量同名的参数或局部变量是不合法的。

### 6.3.7 处理lambda表达式

1.使用lambda表达式的重点是延迟执行（deferred execution）。毕竟，如果想要立即执行代码，完全可以直接执行，而无需把它包装在一个lambda表达式中。之所以希望以后再执行代码，这有很多原因，如：

- 在一个单独的线程中运行代码；
- 多次运行代码； 
- 在算法的适当位置运行代码（例如，排序中的比较操作）； 
- 发生某种情况时执行代码（如，点击了一个按钮，数据到达，等等）； 
- 只在必要时才运行代码。

2.常用函数式接口
Interface |Description
-- | --
`BiConsumer<T,U>`|It represents an operation that accepts two input arguments and returns no result.
`Consumer<T>`|It represents an operation that accepts a single argument and returns no result.
`Function<T,R>`|It represents a function that accepts one argument and returns a result.
`Predicate<T>`|It represents a predicate (boolean-valued function) of one argument.
`BiFunction<T,U,R>`|It represents a function that accepts two arguments and returns a a result.
`BinaryOperator<T>`|It represents an operation upon two operands of the same data type. It returns a result of the same type as the operands.
`BiPredicate<T,U>`|It represents a predicate (boolean-valued function) of two arguments.
`BooleanSupplier`|It represents a supplier of boolean-valued results.
`DoubleBinaryOperator`|It represents an operation upon two double type operands and returns a double type value.
`DoubleConsumer`|It represents an operation that accepts a single double type argument and returns no result.
`DoubleFunction<R>`|It represents a function that accepts a double type argument and produces a result.
`DoublePredicate`|It represents a predicate (boolean-valued function) of one double type argument.
`DoubleSupplier`|It represents a supplier of double type results.
`DoubleToIntFunction`|It represents a function that accepts a double type argument and produces an int type result.
`DoubleToLongFunction`|It represents a function that accepts a double type argument and produces a long type result.
`DoubleUnaryOperator`|It represents an operation on a single double type operand that produces a double type result.
`IntBinaryOperator`|It represents an operation upon two int type operands and returns an int type result.
`IntConsumer`|It represents an operation that accepts a single integer argument and returns no result.
`IntFunction<R>`|It represents a function that accepts an integer argument and returns a result.
`IntPredicate`|It represents a predicate (boolean-valued function) of one integer argument.
`IntSupplier`|It represents a supplier of integer type.
`IntToDoubleFunction`|It represents a function that accepts an integer argument and returns a double.
`IntToLongFunction`|It represents a function that accepts an integer argument and returns a long.
`IntUnaryOperator`|It represents an operation on a single integer operand that produces an integer result.
`LongBinaryOperator`|It represents an operation upon two long type operands and returns a long type result.
`LongConsumer`|It represents an operation that accepts a single long type argument and returns no result.
`LongFunction<R>`|It represents a function that accepts a long type argument and returns a result.
`LongPredicate`|It represents a predicate (boolean-valued function) of one long type argument.
`LongSupplier`|It represents a supplier of long type results.
`LongToDoubleFunction`|It represents a function that accepts a long type argument and returns a result of double type.
`LongToIntFunction`|It represents a function that accepts a long type argument and returns an integer result.
`LongUnaryOperator`|It represents an operation on a single long type operand that returns a long type result.
`ObjDoubleConsumer<T>`|It represents an operation that accepts an object and a double argument, and returns no result.
`ObjIntConsumer<T>`|It represents an operation that accepts an object and an integer argument. It does not return result.
`ObjLongConsumer<T>`|It represents an operation that accepts an object and a long argument, it returns no result.
`Supplier<T>`|It represents a supplier of results.
`ToDoubleBiFunction<T,U>`|It represents a function that accepts two arguments and produces a double type result.
`ToDoubleFunction<T>`|It represents a function that returns a double type result.
`ToIntBiFunction<T,U>`|It represents a function that accepts two arguments and returns an integer.
`ToIntFunction<T>`|It represents a function that returns an integer.
`ToLongBiFunction<T,U>`|It represents a function that accepts two arguments and returns a result of long type.
`ToLongFunction<T>`|It represents a function that returns a result of long type.
`UnaryOperator<T>`|It represents an operation on a single operand that returnsa a result of the same type as its operand.

3.如果设计你自己的接口，其中只有一个抽象方法，可以用@FunctionalInterface注解来标记这个接口。这样做有两个优点。如果你无意中增加了另一个非抽象方法，编译器会产生一个错误消息。另外javadoc页里会指出你的接口是一个函数式接口。并不是必须使用注解。根据定义，任何有一个抽象方法的接口都是函数式接口。不过使用@FunctionalInterface注解确实是一个很好的做法。

### 6.3.8 再谈Comprator

1.Comparator接口包含很多方便的静态方法来创建比较器。这些方法可以用于lambda表达式或方法引用。

2.如果键函数可以返回null，可能就要用到nullsFirst和nullsLast适配器。这些静态方法会修改现有的比较器，从而在遇到null值时不会抛出异常，而是将这个值标记为小于或大于正常值。

## 6.4 内部类

1.内部类（inner class）是定义在另一个类中的类。为什么需要使用内部类呢？其主要原因有以下三点：

- 内部类方法可以访问该类定义所在的作用域中的数据，包括私有的数据。
- 内部类可以对同一个包中的其他类隐藏起来。 
- 当想要定义一个回调函数且不想编写大量代码时，使用匿名（anonymous）内部类比较便捷。

2.嵌套类有两个好处：命名控制和访问控制。由于名字Iterator嵌套在LinkedList类的内部，所以在外部被命名为LinkedList：：Iterator，这样就不会与其他名为Iterator的类发生冲突。在Java中这个并不重要，因为Java包已经提供了相同的命名控制。需要注意的是，Link类位于LinkedList类的私有部分，因此，Link对其他的代码均不可见。鉴于此情况，可以将Link的数据域设计为公有的，它仍然是安全的。这些数据域只能被LinkedList类（具有访问这些数据域的合理需要）中的方法访问，而不会暴露给其他的代码。在Java中，只有内部类能够实现这样的控制。

3.Java内部类还有另外一个功能，内部类的对象有一个隐式引用，它引用了实例化该内部对象的外围类对象。通过这个指针，可以访问外围类对象的全部状态。static内部类没有这种附加指针。

### 6.4.1 使用内部类访问对象状态

1.从传统意义上讲，一个方法可以引用调用这个方法的对象数据域。内部类既可以访问自身的数据域，也可以访问创建它的外围类对象的数据域。内部类的对象总有一个隐式引用，它指向了创建它的外部类对象。这个引用在内部类的定义中是不可见的。

2.外围类的引用在构造器中设置。编译器修改了所有的内部类的构造器，添加一个外围类引用的参数。

3.访问控制。如果有一个类是一个常规类，它可能就需要通过提供一个公有方法给外部类访问它的实例域，而使用内部类可以给予改进，即不必提供仅用于访问其他类的访问器（公有public访问）。

4.只有内部类可以是私有类，而常规类只可以具有包可见性，或公有可见性。

### 6.4.2 内部类的特殊语法规则

1.内部类中声明的所有静态域都必须是final。原因很简单。我们希望一个静态域只有一个实例，不过对于每个外部对象，会分别有一个单独的内部类实例。如果这个域不是final，它可能就不是唯一的。

2.内部类不能有static方法。Java语言规范对这个限制没有做任何解释。也可以允许有静态方法，但只能访问外围类的静态域和方法。显然，Java设计者认为相对于这种复杂性来说，它带来的好处有些得不偿失。

### 6.4.3 内部类是否有用、必要和安全

1.内部类是一种编译器现象，与虚拟机无关。编译器将会把内部类翻译成用$（美元符号）分隔外部类名与内部类名的常规类文件，而虚拟机则对此一无所知。

2.当在Java 1.1的Java语言中增加内部类时，很多程序员都认为这是一项很主要的新特性，但这却违背了Java要比C++更加简单的设计理念。内部类的语法很复杂（匿名内部类更加复杂）。它与访问控制和安全性等其他的语言特性的没有明显的关联。由于增加了一些看似优美有趣，实属没必要的特性，似乎Java也开始走上了许多语言饱受折磨的毁灭性道路上。

3.由于内部类拥有访问特权，所以与常规类比较起来功能更加强大。如果内部类访问了私有数据域，就有可能通过附加在外围类所在包中的其他类访问它们，但做这些事情需要高超的技巧和极大的决心。比如熟悉类文件结构的黑客可以使用十六进制编辑器轻松地创建一个用虚拟机指令调用那个方法的类文件。由于隐秘地访问方法需要拥有包可见性，所以攻击代码需要与被攻击类放在同一个包中。

### 6.4.4 局部内部类

1.可以在一个方法中定义局部类。

2.局部类不能用public或private访问说明符进行声明。它的作用域被限定在声明这个局部类的块中。

3.局部类有一个优势，即对外部世界可以完全地隐藏起来。

### 6.4.5 由外部方法访问变量

1.与其他内部类相比较，局部类还有一个优点。它们不仅能够访问包含它们的外部类，还可以访问局部变量。不过，那些局部变量必须事实上为final。这说明，它们一旦赋值就绝不会改变。

2.从程序员的角度看，局部变量的访问非常容易。它减少了需要显式编写的实例域，从而使得内部类更加简单。

3.在内部类被首次提出时，原型编译器对内部类中修改的局部变量自动地进行转换。不过，后来这种做法被废弃。毕竟，这里存在一个危险。同时在多个线程中执行内部类中的代码时，这种并发更新会导致竞态条件。

### 6.4.6 匿名内部类

1.将局部内部类的使用再深入一步。假如只创建这个类的一个对象，就不必命名了。这种类被称为匿名内部类（anonymous inner class）。

2.由于构造器的名字必须与类名相同，而匿名类没有类名，所以，匿名类不能有构造器。取而代之的是，将构造器参数传递给超类（superclass）构造器。尤其是在内部类实现接口的时候，不能有任何构造参数。不仅如此，还要提供一组括号。

3.使用匿名内部类的解决方案比较简短、更切实际、更易于理解。

4.多年来，Java程序员习惯的做法是用匿名内部类实现事件监听器和其他回调。如今最好还是使用lambda表达式。

### 6.4.7 静态内部类

1.有时候，使用内部类只是为了把一个类隐藏在另外一个类的内部，并不需要内部类引用外围类对象。为此，可以将内部类声明为static，以便取消产生的引用。

2.只有内部类可以声明为static。静态内部类的对象除了没有对生成它的外围类对象的引用特权外，与其他所有内部类完全一样。

3.在内部类不需要访问外围类对象的时候，应该使用静态内部类。

4.与常规内部类不同，静态内部类可以有静态域和方法。

5.声明在接口中的内部类自动成为static和public类。

## 6.5 代理

利用代理可以在运行时创建一个实现了一组给定接口的新类。这种功能只有在编译时无法确定需要实现哪个接口时才有必要使用。对于应用程序设计人员来说，遇到这种情况的机会很少。然而，对于系统程序设计人员来说，代理带来的灵活性却十分重要。

### 6.5.1 何时使用代理

1.假设有一个表示接口的Class对象（有可能只包含一个接口），它的确切类型在编译时无法知道。这确实有些难度。要想构造一个实现这些接口的类，就需要使用newInstance方法或反射找出这个类的构造器。但是，不能实例化一个接口，需要在程序处于运行状态时定义一个新类。 为了解决这个问题，有些程序将会生成代码；将这些代码放置在一个文件中；调用编译器；然后再加载结果类文件。很自然，这样做的速度会比较慢，并且需要将编译器与程序放在一起。而代理机制则是一种更好的解决方案。代理类可以在运行时创建全新的类。这样的代理类能够实现指定的接口。尤其是，它具有下列方法：

- 指定接口所需要的全部方法。
- Object类中的全部方法，例如，toString、equals等。

2.然而，不能在运行时定义这些方法的新代码。而是要提供一个调用处理器（invocation handler）。调用处理器是实现了InvocationHandler接口的类对象。在这个接口中只有一个方法invoke(Object proxy, Method method, Object[] args),无论何时调用代理对象的方法，调用处理器的invoke方法都会被调用，并向其传递Method对象和原始的调用参数。调用处理器必须给出处理调用的方式。

### 6.5.2 创建代理对象

1.要想创建一个代理对象，需要使用Proxy类的newProxyInstance方法。这个方法有三个参数：

- 一个类加载器（class loader）。作为Java安全模型的一部分，对于系统类和从因特网上下载下来的类，可以使用不同的类加载器。目前，用null表示使用默认的类加载器。
- 一个Class对象数组，每个元素都是需要实现的接口。
- 一个调用处理器。
