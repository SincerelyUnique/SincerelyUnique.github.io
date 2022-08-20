---
title: Python中global变量
date: 2022-07-06 15:45:11
tags:
- python
categories:
- [学习, 算法与面试题, 面试题]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 二维数组中的查找

> 题目描述: 判断下面输出。主要考察全局变量的变化情况。

## 题目

```python
name = '123'


def func2():
    print(name)


def func1():
    name = '456'
    func2()


func1()
```

## 答案

输出'123'

## 分析

python中对于全局变量的修改需要先将变量名用global修饰，具体如下，输出为'456'，这样就能改变全局变量的值了。而上面的情况是因为当我们将 456 分配给名称 name 时，Python 会创建一个局部变量来隐藏同名的全局变量。

```python
name = '123'


def func2():
    print(name)


def func1():
    global name
    name = '456'
    func2()


func1()
```

当然，对于列表、字典这种只是修改其中某个元素的值，是可以不用global修饰的，如下会输出['Jady', 'Moe', 'Jones']：

```python
names = ['Jalen', 'Moe', 'Jones']


def func2():
    print(names)


def func1():
    names[0] = 'Jady'
    func2()


func1()
```


