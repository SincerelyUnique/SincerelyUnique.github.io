---
title: JavaScript的值与类型
date: 2017-12-25 08:40:57
tags:
- js
categories:
- [学习, 前端知识学习, JavaScript]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 概述

本文主要来自[GitHub](https://github.com/SincerelyUnique/You-Dont-Know-JS/blob/master/up%20%26%20going/ch2.md)中作者开放的文档

在此书之前第一章中已经提到过，JavaScript中的值有类型之分，而变量是不存在类型的，目前JavaScript拥有如下几种类型，区分null和undefined的话总共7种类型。

- string
- number
- boolean
- null and undefined
- object
- symbol (new to ES6)

如果你不知到某个值的类型，JavaScript里提供了一个名字为typeof的函数，用这个函数可以检测你的值的类型。

```javascript
var a;
typeof a;				// "undefined"
 
a = "hello world";
typeof a;				// "string"
 
a = 42;
typeof a;				// "number"
 
a = true;
typeof a;				// "boolean"
 
a = null;
typeof a;				// "object" -- weird, bug
 
a = undefined;
typeof a;				// "undefined"
 
a = { b: "c" };
typeof a;				// "object"
```

注意上面的6种类型返回的都是string类型的字符串（如果算上es6的"symbol"类型，就是7种），如typeof "abc"返回的是"string"，而不是string。

同时可以看到上面只定义了一个变量，仅是在不同时刻赋予的值是不同的，我们这里使用的typeof a其实际意义上并不是指type of a，而是type of the value current in a，是指值的类型而非变量，JavaScript中只有值拥有类型，而变量只是这些具体值的一个容器。

这其中有一个值的类型比较有趣，那就是typeof null，因为如果你测试一下的话，就会发现她返回的竟然是一个"object"，而你希望她应该正确的返回一个"null"，注意，这是一个由来已久的JavaScript bug，由于现在很多web项目中使用了这个bug，所以不可能再去修复，否则会引发更多的问题.

假如你定义 a = undefined，再定义 var a，当你使用typeof输出a时，会发现其实都是"undefined"，这说明这两种操作可以等同看待，类似这种返回"undefined"的操作还有一些，如将一个没有返回值的函数（即void类型）赋值给a，其返回的也会是一个"undefined"。

```javascript
function fun(){
  
}
var a = fun();
typeof a; // "undefined"
```

下面我们就来仔细看一下

# "object"

首先她是一个复合值，简单说就是她可以包含各种类型的值，也是我们最常用的一个类型。

```javascript
var obj = {
	a: "hello world",
	b: 42,
	c: true
};
 
obj.a;		// "hello world"
obj.b;		// 42
obj.c;		// true
 
obj["a"];	// "hello world"
obj["b"];	// 42
obj["c"];	// true
```

可以抽象成下面这幅图

![](../../../../images/jsl01.png)

注意上面两种获取值的方式，一般优先选择第一种使用点号获取的方式（主要是由于其简短易读的特点）。但是某些情况下，例如以property/key形式定义的值，一般会使用后者获取，如

```javascript
var obj = {
	a: "hello world",
	b: 42
};
 
var b = "a";
 
obj[b];			// "hello world"
obj["b"];		// 42
```

类似地，还有一些常用的类型，如数组array和函数function，这些类型不是内置类型（build-in type），但可以想象成object的子类型。其中数组不是以property/key形式定义的，而是以数字作为下标按一定顺序排列的，以下标0作为起点，如

```javascript
var arr = [
	"hello world",
	42,
	true
];
 
arr[0];			// "hello world"
arr[1];			// 42
arr[2];			// true
arr.length;		// 3
 
typeof arr;		// "object"
```

建议在使用数字定位的值（numerically positioned values）时定义成数组的形式，而在使用具体名字获取值（property/key形式，named properties）时定义成object类型。

function也是object的一个子类型，如下

```javascript
function foo() {
	return 42;
}
 
foo.bar = "hello world";
 
typeof foo;			// "function"
typeof foo();		// "number"
typeof foo.bar;		// "string"
```

注意，通常只是在真正需要时才会调用函数function里的值（如上面定义的foo.bar这种取值方式）。

# 内置类型的方法

在JavaScript中，如果你定义的数据类型是内置类型，那么你可以使用其自带的方法去做一些操作。如字符串的大小写转换，长度大小的获取等。

```javascript
var a = "hello world";
var b = 3.14159;
 
a.length;				// 11
a.toUpperCase();		// "HELLO WORLD"
b.toFixed(4);			// "3.1416"
```

一个字符串值可以被一个String对象包装,一个数字可以被一个Number对象包装，并且一个布尔值可以被一个Boolean对象包装。大多数情况下，你不必担心或直接使用这些值的对象包装形式，基本上我们都是在使用原始数据类型，而不必理会其自身的拆箱和装箱操作。

# 值的比较

（1）强制转换：分为显式和隐式两种，显式转换可以简单地从代码中判断，而隐式的类型转换相对比较隐蔽，可能存在一些不够明显的副作用（或不好的行为）。我们通常会认为强制做类型转换是很邪恶的一种行为，这是因为强制类型转换可能会存在一些出人意料的结果，但事实上，当我们使用强制转换时，很多时候场景是相对合理的，容易被人理解的，甚至可以提高代码的可读性。

显式转换：

```javascript
var a = "42";
 
var b = Number( a );
 
a;				// "42"
b;	
```

隐式转换：

```javascript
var a = "42";
 
var b = a * 1;	// "42" implicitly coerced to 42 here
 
a;				// "42"
b;				// 42 -- the number!
```

（2）布尔值判断（Truthy & Falsy）：当非布尔值被强制转为布尔值时，它是真还是假？下面列一下默认的一些情况。在实际使用中，如果你的ajax返回的数据类型比较复杂，那就要小心了。

默认为falsy的情况：
- "" (empty string)
- 0, -0, NaN (invalid number)
- null, undefined
- false

默认为truthy的情况：
- "hello"
- 42
- true
- [ ], [ 1, "2", 3 ] (arrays)
- { }, { a: 42 } (objects)
- function foo() { .. } (functions)

举个例子说明一下

```javascript
var t = "";
if (t){
 console.info("1");
}else{
 console.info("2");  //输出2
}
```

可以看到默认空字符串做布尔转换时，其实就是false。

（3）相等判断：这个是一个比较常见的问题，一般使用==判断是不做类型区别的，而===会首先判断你的数据类型是否一致，如

```javascript
var a = "42";
var b = 42;
 
a == b;			// true
a === b;		// false
```

所以：
- 如果比较中的任何一个值（aka side）可以是真或假值，则避免==并使用===。
- 如果比较中的任一值可能是这些特定值（0，“”或[] - 空数组），请避免==并使用===。 
- 在所有其他情况下，可以安全地使用==。这不仅是安全的，而且在很多情况下它可以简化代码，提高可读性。

想要具体了解的话可以阅读 [es5官方文档](https://262.ecma-international.org/5.1/)的11.9.3章节。

另外，在做数组判断时，可能会遇到下面这种情况。至于原因是什么，可以参考es5的官方文档。

```javascript
var a = [1,2,3];
var b = [1,2,3];
var c = "1,2,3";
 
a == c;		// true
b == c;		// true
a == b;		// false
```

（4）不等式判断：通常我们认为做不等判别的知识number类型，然而在JavaScript中，string类型值同样可以，string类型的判断使用的是字母排列规则，如b<f，a<b

```javascript
console.info("bar" < "foo");   //输出true
```

```javascript
console.info("24" < "21");   //输出false
```

当然，你也可以将number和string做比较，如

```javascript
var a = 41;
var b = "42";
var c = "43";
 
a < b;		// true
b < c;		// true
```

然而，有时我们会遇到比较奇葩的判断，致使两边的数据类型“严格不想等”（strict inequality），如

```javascript
var a = 42;
var b = "foo";
 
a < b;		// false
a > b;		// false
a == b;		// false
```

此时，上面的b会判定为"invalid number value" ，即NaN，在es5文档上讲：NaN既不大于也不小于任何其他值。所以都是false。


