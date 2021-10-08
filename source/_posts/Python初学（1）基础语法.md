---
title: Python初学（1）基础语法
date: 2018-04-13 09:32:24
tags:
- python
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

python版本：2.7.13

在jetbrain官网下载pycharm，我的是2018.1.1

参考https://blog.csdn.net/qq_35246620/article/details/79050895

```python
# -*- coding: UTF-8 -*-
import sys
 
# 如果这里不加第一行编码设置，中文输出会报错
print("Hello,World")
print("你好，世界")
 
 
# python 会自动换行
print "Hello, Python!";print "Hello, Python!"
x = 'Cupid'; sys.stdout.write(x + '\n')
 
 
# python 对格式要求非常严格,缩进方式必须一致
a = 2
b = 3
c = a * b
if c==6:
    print("Hello,true")
    print("true,yes")
else:
    print("Hello,false")
    print("false,yes")
 
 
# 多行语句
total = "I" + \
        " am" + \
        " a" + \
        " student"
days = ['Monday', 'Tuesday', 'Wednesday',
        'Thursday', 'Friday']
print(total);print(days)
 
 
# 引号( ' )、双引号( " )、三引号( ''' 或 """ ) 表示字符串
word = 'word'
sentence = "这是一个句子。"
paragraph = """这是一个段落。
包含了多个语句"""
print(word);print(sentence);print(paragraph)
# 单行注释采用 # 开头,多行注释使用三个单引号(''')或三个双引号(""")
name = "Mash"  # 这是一个注释
'''
注释
注释
注释
'''
"""
注释
注释
注释
"""
 
 
# 等待用户输入
raw_input("按下 enter 键退出，其他任意键显示...\n")
 
 
# 输出
x="a"
y="b"
# 换行输出
print x
print y
# 不换行输出
print x,
print y,
# 不换行输出
print x,y
```

输出

```
Hello,World
你好，世界
Hello, Python!
Hello, Python!
Cupid
Hello,true
true,yes
I am a student
['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday']
word
这是一个句子。
这是一个段落。
包含了多个语句
按下 enter 键退出，其他任意键显示...
1
a
b
a b a b
```

注：

1.每行代码敲完，光标都要切换到下一行，否则会有警告

2.空白行一般最多2行，再多会有警告

3.pycharm默认使用pep8代码格式检测，如果不爽可以ctrl+enter忽略

4.代码结尾不要加分号";",会有警告
