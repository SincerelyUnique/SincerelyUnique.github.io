---
title: Python中异常的相关用法
date: 2022-07-06 15:45:11
tags:
- python
categories:
- [学习, 算法与面试题, 面试题]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 二维数组中的查找

> 题目描述: 判断下面输出。主要考察异常处理时执行顺序。

## 题目

```python
def divide(a, b):
    try:
        return a/b
    except Exception:
        return -2
    finally:
        return -1


print(divide(2, 1), divide(2, 0))
```

## 答案

输出-1，-1

## 分析

try-finally 语句 是 无论是否发生异常都将执行最后的代码。也就是如果发生异常，会先走异常处理分支，走完异常处理分支后会走finally分支。
