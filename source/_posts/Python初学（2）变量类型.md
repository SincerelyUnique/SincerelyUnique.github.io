---
title: Python初学（2）变量类型
date: 2018-04-13 11:12:12
tags:
- python
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
# -*- coding: UTF-8 -*-
 
 
# 变量赋值
counter = 100  # 赋值整型变量
miles = 1000.0  # 浮点型
name = "John"  # 字符串
print counter;print miles;print name
print("\n")
 
 
# 多个变量赋值
a = b = c = 1
d, e, f = 1, 2, "john"
print(a);print(b);print(c);print(d);print(e);print(f)
print("\n")
 
 
# 五个标准的数据类型
# Numbers（数字）
number1 = 10
number2 = 10.1
number3 = 0.2
# String（字符串）
string = "kite"
# List（列表）
list1 = ['physics', 'chemistry', 1997, 2000]
list2 = [1, 2, 3, 4, 5]
list3 = ["a", "b", "c", "d"]
# Tuple（元组）
tup1 = ('physics', 'chemistry', 1997, 2000)
tup2 = (1, 2, 3, 4, 5)
tup3 = "a", "b", "c", "d"
# Dictionary（字典）
dict1 = {'Alice': '2341', 'Beth': '9102', 'Cecil': '3258'}
dict2 = {'abc': 456}
dict3 = {'abc': 123, 98.6: 37}
print("\n")
 
 
# Python数字
# int（有符号整型）,long（长整型[也可以代表八进制和十六进制]）,float（浮点型）,complex（复数）
var1 = 1  # int
var2 = 10  # int
del var2  # 删除var2
var3 = 51924361L  # long
var4 = 15.20  # float
var5 = 3.14j  # complex
var6 = 9.322e-36j  # complex
var7 = complex(3,26)  # 3e+26J
print("\n")
 
 
# Python 判空
nullString = ""
if nullString.strip()=='':
    print("string is null")
else:
    print("string isn't null")
 
string = "123"
if string.strip()=="":
    print("string is null")
if string.strip():
    print("string isn't null")
print("\n")
 
 
# Python字符串
strVal = 'Hello World!'
print strVal  # 输出完整字符串
print strVal[0]  # 输出字符串中的第一个字符
print strVal[2:5]  # 输出字符串中第三个至第五个之间的字符串
print strVal[2:]  # 输出从第三个字符开始的字符串
print strVal * 2  # 输出字符串两次
print strVal + "TEST"  # 输出连接的字符串
print("\n")
 
 
# Python列表
list0 = ['button', 786, 2.23, 'john', 70.2]
tinyList = [123, 'john']
print list0  # 输出完整列表
print list0[0]  # 输出列表的第一个元素
print list0[1:3]  # 输出第二个至第三个元素
print list0[2:]  # 输出从第三个开始至列表末尾的所有元素
print tinyList * 2  # 输出列表两次
print list0 + tinyList  # 打印组合的列表
list0[2] = 1000     # 列表中是合法应用
print list0
print("\n")
 
 
# Python元组:类似于List（列表）,但是元组不能二次赋值,即只读
tup = ('button', 786, 2.23, 'john', 70.2)
tinyTuple = (123, 'john')
print tup  # 输出完整元组
print tup[0]  # 输出元组的第一个元素
print tup[1:3]  # 输出第二个至第三个的元素
print tup[2:]  # 输出从第三个开始至列表末尾的所有元素
print tinyTuple * 2  # 输出元组两次
print tup + tinyTuple  # 打印组合的元组
# tup[2] = 1000    # 元组中是非法应用
print("\n")
 
 
# Python 字典
dictionary = dict()
dictionary['one'] = "This is one"
dictionary[2] = "This is two"
tinyDict = {'name': 'john', 'code': 6734, 'dept': 'sales'}
print dictionary['one']  # 输出键为'one' 的值
print dictionary[2]  # 输出键为 2 的值
print tinyDict  # 输出完整的字典
print tinyDict.keys()  # 输出所有键
print tinyDict.values()  # 输出所有值
print("\n")
 
 
# Python数据类型转换
data1 = int(1.2);print(data1);print(type(data1))   # 将x转换为一个整数
data2 = long(98);print(data2);print(type(data2))   # 将x转换为一个长整数
data3 = float(1.5);print(data3);print(type(data3))  # 将x转换到一个浮点数
data4 = complex(12);print(data4);print(type(data4))  # 创建一个复数
data5 = str(12);print(data5);print(type(data5))     # 将对象 x 转换为表达式字符串
data6 = eval(str(14));print(data6);print(type(data6))  # 用来计算在字符串中的有效Python表达式,并返回一个对象
data7 = tuple("1234");print(data7);print(type(data7))  # 将序列 s 转换为一个元组
data8 = list("1234");print(data8);print(type(data8))  # 将序列 s 转换为一个列表
data9 = set("1234");print(data9);print(type(data9))   # 转换为可变集合
data10 = dict(a='1', b='2', t='3');print(data10);print(type(data10))  # 创建一个字典。d 必须是一个序列 (key,value)元组。
data11 = frozenset("1234");print(data11);print(type(data11))  # 转换为不可变集合
data12 = chr(65);print(data12);print(type(data12))  # 将一个整数转换为一个字符,数字65的Ascii码为A
data13 = unichr(97);print(data13);print(type(data13))  # 将一个整数转换为Unicode字符
data14 = ord("a");print(data14);print(type(data14))  # 将一个字符转换为它的整数值
data15 = hex(10);print(data15);print(type(data15))  # 将一个整数转换为一个十六进制字符串
data16 = oct(10);print(data16);print(type(data16))  # 将一个整数转换为一个八进制字符串
```

```python
100
1000.0
John
 
 
1
1
1
1
2
john
 
 
string is null
string isn't null
 
 
Hello World!
H
llo
llo World!
Hello World!Hello World!
Hello World!TEST
 
 
['button', 786, 2.23, 'john', 70.2]
button
[786, 2.23]
[2.23, 'john', 70.2]
[123, 'john', 123, 'john']
['button', 786, 2.23, 'john', 70.2, 123, 'john']
['button', 786, 1000, 'john', 70.2]
 
 
('button', 786, 2.23, 'john', 70.2)
button
(786, 2.23)
(2.23, 'john', 70.2)
(123, 'john', 123, 'john')
('button', 786, 2.23, 'john', 70.2, 123, 'john')
 
 
This is one
This is two
{'dept': 'sales', 'code': 6734, 'name': 'john'}
['dept', 'code', 'name']
['sales', 6734, 'john']
 
 
1
<type 'int'>
98
<type 'long'>
1.5
<type 'float'>
(12+0j)
<type 'complex'>
12
<type 'str'>
14
<type 'int'>
('1', '2', '3', '4')
<type 'tuple'>
['1', '2', '3', '4']
<type 'list'>
set(['1', '3', '2', '4'])
<type 'set'>
{'a': '1', 'b': '2', 't': '3'}
<type 'dict'>
frozenset(['1', '3', '2', '4'])
<type 'frozenset'>
A
<type 'str'>
a
<type 'unicode'>
97
<type 'int'>
0xa
<type 'str'>
012
<type 'str'>
```
