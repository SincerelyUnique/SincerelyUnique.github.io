---
title: Python初学（4）基本语句
date: 2018-04-13 14:50:48
tags:
- python
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

条件语句

```python
# -*- coding: UTF-8 -*-
 
# 条件语句：例1：if 基本用法
flag = False
name = 'Bob'
if name == 'python':         # 判断变量否为'python'
    flag = True          # 条件成立时设置标志为真
    print 'welcome boss'    # 并输出欢迎信息
else:
    print name              # 条件不成立时输出变量名称
print("\n")
 
 
# 例2：elif用法
num = 5
if num == 3:  # 判断num的值
    print 'boss'
elif num == 2:
    print 'user'
elif num == 1:
    print 'worker'
elif num < 0:  # 值小于零时输出
    print 'error'
else:
    print 'a'  # 条件均不成立时输出
print("\n")
 
 
# 例3：if语句多个条件
num = 9
if 0 <= num <= 10:  # 判断值是否在0~10之间
    print 'hello'
num = 10
if num < 0 or num > 10:  # 判断值是否在小于0或大于10
    print 'hello'
else:
    print 'undefined'
num = 8
if (0<= num <= 5) or (10<= num <= 15):  # 判断值是否在0~5或者10~15之间
    print 'hello'
else:
    print 'undefined'
print("\n")
 
 
# 可以在同一行的位置上使用if条件判断语句
var = 100
if var == 100:
    print "变量 var 的值为100"
print "Good bye!"
```

循环语句

```python
# -*- coding: UTF-8 -*-
 
# 循环语句：while，break & continue，无限循环
count = 0
while count < 9:
    print 'The count is:', count
    count = count + 1
print "--------------------"
i = 1
while i < 10:
    i += 1
    if i % 2 > 0:  # 非双数时跳过输出
        continue
    print i  # 输出双数2、4、6、8、10
print "--------------------"
i = 1
while 1:  # 循环条件为1必定成立
    print i  # 输出1~10
    i += 1
    if i > 10:  # 当i大于10时跳出循环
        break
print "--------------------"
var = 1
while var == 2:  # 如果var==1，则该条件永远为true，循环将无限执行下去
    num = raw_input("Enter a number  :")
    print "You entered: ", num
print "--------------------"
count = 0
while count < 5:
    print count, " is  less than 5"
    count = count + 1
else:
    print count, " is not less than 5"
print "--------------------"
flag = 0  # 如果flag为1，则无限循环打印
while flag:
    print 'Given flag is really true!'
print "--------------------"
# 输出 Python 的每个字母
for letter in 'Python':
    if letter == 'h':
        pass
        print '这是 pass 块'
    print '当前字母 :', letter
 
print "Good bye!"
```

for语句

```python
# -*- coding: UTF-8 -*-
 
 
# 字符串遍历
for letter in 'Python':
    print '当前字母 :', letter
print "--------------------"
 
 
# 列表遍历
fruits = ['banana', 'apple', 'mango']
for fruit in fruits:    # foreach
    print '当前水果 :', fruit
for index in range(len(fruits)):    # 通过索引遍历
    print '当前水果 :', fruits[index]
print "--------------------"
# for … else
for num in range(10,20):    # 迭代 10 到 20 之间的数字
    for i in range(2,num):  # 根据因子迭代
        if num % i == 0:    # 确定第一个因子
            j=num/i         # 计算第二个因子
            print '%d 等于 %d * %d' % (num,i,j)
            break           # 跳出当前循环
    else:                   # 循环的 else 部分
        print num, '是一个质数'
```

嵌套语句

```python
# -*- coding: UTF-8 -*-
 
 
# while循环嵌套：输出2~100之间的素数
i = 2
while i < 100:
    j = 2
    while j <= (i / j):
        if not (i % j): break
        j = j + 1
    if j > i / j: print i, " 是素数"
    i = i + 1
 
print "---------------------"
 
 
# for循环嵌套：
listVal = ["kite","bob","john"]
for name in listVal:
    for letter in name:
        print(letter)
```


