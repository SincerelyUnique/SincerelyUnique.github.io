---
title: Python中深浅拷贝
date: 2022-07-06 15:45:11
tags:
- python
categories:
- [学习, 算法与面试题, 面试题]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 二维数组中的查找

> 题目描述: 简述python中深浅拷贝的区别。

## 练习

```python
# 下面改变b的第一个元素后，a的第一个元素也变化了, 使用id查看两对象是一个
a = [1, 2, 3]
b = a
b[0] = 4
print(b, id(b))  # [4, 2, 3] 1722827161800
print(a, id(a))  # [4, 2, 3] 1722827161800


# 改变b的第一个元素后a不影响，id查看是两个对象
import copy
a = [1, 2, 3]
b = copy.copy(a)
b[0] = 4
print(b, id(b))  # [4, 2, 3] 1722859718536
print(a, id(a))  # [1, 2, 3] 1722859718728


# 和上面copy模块的使用一样效果
a = [1, 2, 3]
b = a.copy()
b[0] = 4
print(b, id(b))  # [4, 2, 3] 1722859718728
print(a, id(a))  # [1, 2, 3] 1722827161800


# 虽然是两个对象，但是发现a的第一个元素不受影响，但是a的第三个元素，也就是字典里的元素收到了影响，所以这是个浅拷贝
import copy
a = [1, 2, {'name': 'Jalen'}]
b = copy.copy(a)
b[0] = 4
b[2]['name'] = 'Moe'
print(b, id(b))  # [4, 2, {'name': 'Moe'}] 1722827161800
print(a, id(a))  # [1, 2, {'name': 'Moe'}] 1722859718536


# 发现a对象字典里的元素也不受b的影响
import copy
a = [1, 2, {'name': 'Jalen'}]
b = copy.deepcopy(a)
b[0] = 4
b[2]['name'] = 'Moe'
print(b, id(b))  # [4, 2, {'name': 'Moe'}] 1722859718536
print(a, id(a))  # [1, 2, {'name': 'Jalen'}] 1722859718728


# 再深入修改数据元素，测试深拷贝，发现a完全不受影响
import copy
a = [1, 2, {'name': {'china': ['anhui', 'jiangsu', 'zhejiang']}}]
b = copy.deepcopy(a)
b[0] = 4
b[2]['name']['china'][0] = 'hubei'
print(b, id(b))  # [4, 2, {'name': {'china': ['hubei', 'jiangsu', 'zhejiang']}}] 1722859718728
print(a, id(a))  # [1, 2, {'name': {'china': ['anhui', 'jiangsu', 'zhejiang']}}] 1722858518408
```

## 答案

浅拷贝仅拷贝上层元素，如果拷贝后的对象有变化，则深层元素受影响；深拷贝完整复制，修改拷贝过后对象不会对原来对象带来任何影响。
