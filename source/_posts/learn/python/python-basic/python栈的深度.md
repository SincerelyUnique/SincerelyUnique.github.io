---
title: python栈的深度
date: 2019-01-28 15:08:00
tags:
- python
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 测试python默认允许的栈的深度，执行后输出到999后报RuntimeError异常，说明栈深默认为999

```python
def foo(n):
    print(n)
    n += 1
    foo(n)
 
 
if __name__ == '__main__':
    foo(1)
```

2. 解除改限制，如设置递归限制为1200，此时栈深1199

```python
def foo(n):
    print(n)
    n += 1
    foo(n)
 
 
if __name__ == '__main__':
    import sys
    sys.setrecursionlimit(1200)
    foo(1)
```
