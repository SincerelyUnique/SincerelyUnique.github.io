---
title: Python初学（3）运算符
date: 2018-04-13 11:58:31
tags:
- python
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
# -*- coding: UTF-8 -*-
 
 
# 1.Python算术运算符
a = 21
b = 10
 
c = a + b
print "a + b 的值为：", c
 
c = a - b
print "a - b 的值为：", c
 
c = a * b
print "a * b 的值为：", c
 
# Python2.x 里，整数除整数，只能得出整数。如果要得到小数部分，把其中一个数改成浮点数即可
c = a / b
print "a / b 的值为：", c
d = 10.0
c = a / d
print "a / d 的值为：", c
 
c = a % b
print "取模 - 返回除法的余数：", c
 
# 修改变量 a 、b 、c
a = 2
b = 3
c = a ** b
print "a的b次幂：", c
 
a = 10
b = 5
c = a // b
print "取整除 - 返回商的整数部分：", c
print("\n")
 
 
# 2.Python比较运算符
a = 21
b = 10
c = 0
 
if a == b:
    print "a 等于 b"
else:
    print "a 不等于 b"
 
if a != b:
    print "a 不等于 b"
else:
    print "a 等于 b"
 
# 废弃
'''
if a <> b:
    print "a 不等于 b"
else:
    print "a 等于 b"
'''
 
if a < b:
    print "a 小于 b"
else:
    print "a 大于等于 b"
 
if a > b:
    print "a 大于 b"
else:
    print "a 小于等于 b"
 
# 修改变量 a 和 b 的值
a = 5
b = 20
if a <= b:
    print "a 小于等于 b"
else:
    print "a 大于  b"
 
if b >= a:
    print "b 大于等于 a"
else:
    print "b 小于 a"
print("\n")
 
 
# 3.Python赋值运算符
a = 21
b = 10
 
c = a + b
print "a + b的值为：", c
 
c += a
print "c += a的值为：", c
 
c *= a
print "c *= a的值为：", c
 
c /= a
print "c /= a的值为：", c
 
c = 2
c %= a
print "c %= a的值为：", c
 
d = 4
c **= d
print "c **= d的值为：", c
 
c //= a
print "c //= a的值为：", c
print("\n")
 
 
# 4.Python位运算符
a = 60  # 60 = 0011 1100
b = 13  # 13 = 0000 1101
 
c = a & b  # 12 = 0000 1100
print "a & b的值为：", c
 
c = a | b  # 61 = 0011 1101
print "a | b的值为：", c
 
c = a ^ b  # 49 = 0011 0001
print "a ^ b的值为：", c
 
c = ~a  # -61 = 1100 0011
print "~a的值为：", c
 
c = a << 2  # 240 = 1111 0000
print "a << 2的值为：", c
 
c = a >> 2  # 15 = 0000 1111
print "a >> 2的值为：", c
print("\n")
 
 
# 5.Python逻辑运算符：布尔"与"，布尔"或"，布尔"非"
a = 10
b = 20
 
if a and b:
    print "变量 a 和 b 都为 true"
else:
    print "变量 a 和 b 有一个不为 true"
 
if a or b:
    print "变量 a 和 b 都为 true，或其中一个变量为 true"
else:
    print "变量 a 和 b 都不为 true"
 
# 修改变量 a 的值
a = 0
if a and b:
    print "变量 a 和 b 都为 true"
else:
    print "变量 a 和 b 有一个不为 true"
 
if a or b:
    print "变量 a 和 b 都为 true，或其中一个变量为 true"
else:
    print "变量 a 和 b 都不为 true"
 
if not (a and b):
    print "变量 a 和 b 都为 false，或其中一个变量为 false"
else:
    print "变量 a 和 b 都为 true"
print("\n")
 
 
# 6.Python成员运算符:以列表成员为例
a = 10
b = 20
listData = [1, 2, 3, 4, 5]
 
if a in listData:
    print "变量 a 在给定的列表中 list 中"
else:
    print "变量 a 不在给定的列表中 list 中"
 
if b not in listData:
    print "变量 b 不在给定的列表中 list 中"
else:
    print "变量 b 在给定的列表中 list 中"
 
# 修改变量 a 的值
a = 2
if a in listData:
    print "变量 a 在给定的列表中 list 中"
else:
    print "变量 a 不在给定的列表中 list 中"
print("\n")
 
 
# 7.Python身份运算符:用于比较两个对象的存储单元
# is 与 == 区别：is 用于判断两个变量引用对象是否为同一个， == 用于判断引用变量的值是否相等。
a = 20
b = 20
 
if a is b:
    print "a 和 b 有相同的标识"
else:
    print "a 和 b 没有相同的标识"
 
if a is not b:
    print "a 和 b 没有相同的标识"
else:
    print "a 和 b 有相同的标识"
 
# 修改变量 b 的值
b = 30
if a is b:
    print "a 和 b 有相同的标识"
else:
    print "a 和 b 没有相同的标识"
 
if a is not b:
    print "a 和 b 没有相同的标识"
else:
    print "a 和 b 有相同的标识"
print("\n")
 
 
# Python运算符优先级
a = 20
b = 10
c = 15
d = 5
 
e = (a + b) * c / d  # ( 30 * 15 ) / 5
print "(a + b) * c / d 运算结果为：", e
 
e = ((a + b) * c) / d  # (30 * 15 ) / 5
print "((a + b) * c) / d 运算结果为：", e
 
e = (a + b) * (c / d)  # (30) * (15/5)
print "(a + b) * (c / d) 运算结果为：", e
 
e = a + (b * c) / d  # 20 + (150/5)
print "a + (b * c) / d 运算结果为：", e
```

```
a + b 的值为： 31
a - b 的值为： 11
a * b 的值为： 210
a / b 的值为： 2
a / d 的值为： 2.1
取模 - 返回除法的余数： 1
a的b次幂： 8
取整除 - 返回商的整数部分： 2
 
 
a 不等于 b
a 不等于 b
a 大于等于 b
a 大于 b
a 小于等于 b
b 大于等于 a
 
 
a + b的值为： 31
c += a的值为： 52
c *= a的值为： 1092
c /= a的值为： 52
c %= a的值为： 2
c **= d的值为： 16
c //= a的值为： 0
 
 
a & b的值为： 12
a | b的值为： 61
a ^ b的值为： 49
~a的值为： -61
a << 2的值为： 240
a >> 2的值为： 15
 
 
变量 a 和 b 都为 true
变量 a 和 b 都为 true，或其中一个变量为 true
变量 a 和 b 有一个不为 true
变量 a 和 b 都为 true，或其中一个变量为 true
变量 a 和 b 都为 false，或其中一个变量为 false
 
 
变量 a 不在给定的列表中 list 中
变量 b 不在给定的列表中 list 中
变量 a 在给定的列表中 list 中
 
 
a 和 b 有相同的标识
a 和 b 有相同的标识
a 和 b 没有相同的标识
a 和 b 没有相同的标识
 
 
(a + b) * c / d 运算结果为： 90
((a + b) * c) / d 运算结果为： 90
(a + b) * (c / d) 运算结果为： 90
a + (b * c) / d 运算结果为： 50
```


