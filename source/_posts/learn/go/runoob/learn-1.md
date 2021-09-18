---
title: 第一节 安装及简单使用
date: 2020/08/13 20:56:48
categories:
- [学习, Go语言学习, 菜鸟教程 - Go语言]
tags:
- Go
- 教程
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Go语言学习

简单学习一下go语言，因为工作涉及，并且最近几年go语言发展比较快，用到的地方也比较多，不过更多都是用在devops相关业务上。

# 安装
安装比较简单啦，我用的是windows，下载amd64的msi安装报直接安装即可，安装后会自动在系统高级环境设置中添加go的环境变量到path中，所以很简单。

# 简单程序示例
```go
package main

import "fmt"

func main() {
	fmt.Println("Hello World")
}

func init() {
	fmt.Println("你好呀")
}
```
这里做下解释：
1. 定义当前程序的包的名字，如果想要写一个go文件并作为可运行的独立文件，必须要导入main包，不导入main包或者导入其他比如名字叫mains的包都会执行报错，这里执行指的是在cmd命令行下运行`go run hello.go`报错。每个go应用程序都包含一个名字叫main的包。
2. 导入fmt包（或者函数啥的，也有可能是其他的），fmt是我们在下面程序里需要用到的格式化输入、输出的函数，这个在python或者java里都有自己的输出方式，不过不需要显式的导入，这里竟然需要显式的导入进来，比较新奇。
3. main是程序开始执行的函数，这个在各个语言中都差不多，main函数是每个可执行程序都必须的，是程序的入口（一般来说，如果有init方法，会先执行init方法），init函数和main函数有些差别，主要体现在使用上，可以自行百度。
4. 注释很简单，略~~
5. fmt.Println()注意这里的Println函数的首字母是大写的P，go语言里如果函数、常量、变量等以大写字母开头，表示可以被外部包里的程序调用，相当于java里的public修饰符，如果第一个字母小写，相当于java里的protect修饰符（java的默认修饰符），主要影响程序的调用和可见性。
6. 程序可以直接使用`go run hello.go`运行得到输出，也可以先编译在运行，比如`go build hello.go`windows编译后会得到一个.exe的可执行文件，然后`.\hello.exe`执行，能够得到同样的输出。
7. 同一个文件夹下面所有文件的package name必须是一样的（package name不一定非得和文件夹名字一样），否则执行时会报错。

# 导入自定义包

## 目录
```
--go-learning
----demo
------demo01.go
------demo02.go
----hello.go
```
```go

## 代码
/* hello.go */
package main

import (
	"fmt"

	math "./demo"
)

func main() {
	fmt.Println("Hello World")
	fmt.Println(math.Add(1, 1))
	fmt.Println(math.Sub(1, 1))
}
```
```go
/* demo01.go */
package math

func Add(x, y int) int {
	return x + y
}

func div(x, y int) int {
	return x / y
}
```
```go
/* demo02.go */
package math

func Sub(x, y int) int {
	var z = Add(2, 4)  // 同文件夹内可直接调用
	var s = div(9, 3)
	return x - y + z + s
}
```
注意上面导入的使用即可

# 声明变量与格式化输出
```go
package main

import (
	"fmt"
)

func main() {
	var name = "jalen"
	var age = 12
	var height = 178.23
	var total = fmt.Sprintf("name=%s; age=%d; height=%f", name, age, height)
	fmt.Println(total)
}
```

# 数据类型
布尔型（true，false）、数字类型（int，float32，float64，complex）、字符串类型、派生类型。
派生类型包含：pointer、array、struct、channel、func、切片、interface、map

# 变量声明
> var identifier type

```go
/* hello.go */
package main

import (
	"fmt"

	math "./demo"
)

func main() {
	fmt.Println("Hello World")
	fmt.Println(math.Add(1, 1))
	fmt.Println(math.Sub(1, 1))

	var name string = "Jalen"
	fmt.Println(name)

	var age int = 12
	fmt.Println(age)

	var username, password string = "Jalen", "@"
	fmt.Println(username, password)

	siteId, siteName := 12, "jalen.site"
	fmt.Println(siteId, siteName)

	a, b := 10, 20
	a, b = b, a
	fmt.Println(a, b)

	_, numb, strs := numbers()
	fmt.Println(numb, strs)
}

func numbers() (int, int, string) {
	a, b, c := 1, 2, "str"
	return a, b, c
}
```

# 常量声明
```go
//const identifier [type] = value
const LENGTH int = 10

const (
    Unknown = 0
    Female = 1
    Male = 2
)

const (
    a = iota  //=0
    b = iota  //=1
    c = iota  //=2
)

const (
    a = iota //0
    b        //1
    c        //2
    d = "ha" //独立值，iota += 1
    e        //"ha"   iota += 1
    f = 100  //iota +=1
    g        //100  iota +=1
    h = iota //7,恢复计数
    i        //8
)

const (
    a = 1
    b  //=1
    c  //=1
    d  //=1
)
```

# 语言运算符
运算符和运算符优先级同其他语言基本相同，注意指针变量*与取地址赋值
```go
/* hello.go */
package main

func main() {
	var a int = 4
	var ptr *int = &a
	println("a的值为", a)     // 4
	println("*ptr为", *ptr) // 4
	println("ptr为", ptr)   // 824633794744
}
```
注意a++和a--这种自增自减运算不能用于赋值语句，只能作为表达式存在
```go
/* hello.go */
package main

func main() {
	a++ // 这是允许的，类似 a = a + 1,结果与 a++ 相同
	a-- //与 a++ 相似
	a = a++ // 这是不允许的，会出现编译错误 syntax error: unexpected ++ at end of statement
}
```

# 条件语句
1. if
2. if...else...
3. if...else if...else...
4. switch
5. select

注意Go 没有三目运算符，所以不支持 ?: 形式的条件判断。
select比较新奇，随机执行一个可运行的case，没有则阻塞挂起。
```go
select {
    case communication clause  :
       statement(s);      
    case communication clause  :
       statement(s);
    /* 你可以定义任意数量的 case */
    default : /* 可选 */
       statement(s);
}
```

# 循环语句
1. break中断（for、switch中断）
2. continue （跳过）
3. goto （标记跳转）

# 语言函数
Go 语言最少有个 main() 函数。
```
func function_name( [parameter list] ) [return_types] {
   函数体
}
```

















# 遇到的问题
1. The term 'go' is not recognized as the name of a cmdlet, function, script file, or operable program 这个是vscode报错
我是先开的vscode写go，写好后发现go环境没有安装，安装好go环境配置环境变量后重启vscode即可

2. build command-line-arguments: cannot find module for path xxx 这个是新建文件夹，在文件夹下面新建了2个简单文件，然后从外部引入这个文件夹内的包，执行`go run hello.go`时报错，windows在外部cmd下执行`go env -w GO111MODULE=auto`(老版本可能是`go env -w set GO111MODULE=auto`)
