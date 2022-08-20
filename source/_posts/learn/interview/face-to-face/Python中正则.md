---
title: Python中正则模块match和search区别
date: 2022-07-06 15:45:11
tags:
- python
categories:
- [学习, 算法与面试题, 面试题]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 二维数组中的查找

> 题目描述: 讲述一下python中正则re模块下search和match区别。

## 练习

```python
if __name__ == '__main__':
    import re
    string = 'cat like fish, rabbit like carrot.'
    sub_string = re.sub('like', 'hate', string, flags=re.I)
    print(sub_string)  # 输出：cat hate fish, rabbit hate carrot.

    search_string = re.search('like', string, flags=re.I)
    print(search_string)  # 输出：<_sre.SRE_Match object; span=(4, 8), match='like'>

    match_string1 = re.match('like', string, flags=re.I)
    match_string2 = re.match('.*(like).*', string, flags=re.I)
    match_string3 = re.match('cat', string, flags=re.I)
    match_string4 = re.match('.*(fish|carrot).*', string, flags=re.I).groups()
    print(match_string1)  # 输出：None
    print(match_string2)  # 输出：<_sre.SRE_Match object; span=(0, 34), match='cat like fish, rabbit like carrot.'>
    print(match_string3)  # 输出：<_sre.SRE_Match object; span=(0, 3), match='cat'>
    print(match_string4)  # 输出：('carrot',)

    find_string1 = re.findall('like', string, flags=re.I)
    find_string2 = re.findall('like|carrot', string, flags=re.I)
    print(find_string1)  # 输出：['like', 'like']
    print(find_string2)  # 输出：['like', 'like', 'carrot']
    print('End!')
```

## 分析

1. re.sub用于替换字符串
2. re.search用于查找字符串，无论该字符串出现在开始还是中间，只要出现就会返回match对象，match对象里有出现的位置
3. re.match用于查找字符串，如果不是正则pattern，只从头搜索（或者说只匹配字符串的开始字符），如果match不上返回None
4. re.findall用于查找字符串，返回一个搜索到的目标list
5. re.match有1个groups方法，可以返回一个元组对象
