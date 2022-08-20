---
title: python修改OrderedDict的key且不改变排列顺序
date: 2020-03-26 17:55:13
tags:
- python
categories:
- [学习, Python语言学习, Python]
---

```python
if __name__ == '__main__':
    d = collections.OrderedDict([('a', 1), ('c', 3), ('b', 2)])
    print(d)
    d2 = collections.OrderedDict([('__C__', v) if k == 'c' else (k, v) for k, v in d.items()])
    print(d2)
```

```
输出：
OrderedDict([('a', 1), ('c', 3), ('b', 2)])
OrderedDict([('a', 1), ('__C__', 3), ('b', 2)])
```

参考：https://stackoverflow.com/questions/12150872/change-key-in-ordereddict-without-losing-order
