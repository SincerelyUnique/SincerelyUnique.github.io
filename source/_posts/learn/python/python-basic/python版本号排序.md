---
title: python版本号排序
date: 2020-08-11 10:48:38
tags:
- python
categories:
- [学习, Python语言学习, Python]
---

## 方法一（纯数字或带字母），使用distutils下自带的工具

```python
from distutils.version import LooseVersion
from distutils.version import StrictVersion
```

## 方法二（纯数字），版本号类似

> ['1.0.0', '1.0.2', '1.0.12', '1.1.2', '1.3.3']

```python
import os
import sys
 
basedir = os.path.abspath(os.path.dirname(__file__))
lib_path = os.path.dirname(os.path.dirname(basedir))
sys.path.append(lib_path)
 
from demo import logger
 
if __name__ == '__main__':
    order = sys.argv[1]
    logger.info('Order is [ %s ].' % order)
    version_list = ["1.1.2", "1.0.0", "1.3.3", "1.0.12", "1.0.2"]
    if order == 'asc':
        version_list.sort(key=lambda s: map(int, s.split('.')))
    if order == 'desc':
        version_list.sort(key=lambda s: map(int, s.split('.')), reverse=True)
    logger.info('sort versions is [ %s ].' % version_list)
```

```
(venv) C:\Users\jalen\demo>python dashboard\test\commontest.py asc
2020-08-11 02:44:33,391 commontest.py:12:INFO:Order is [ asc ].
2020-08-11 02:44:33,392 commontest.py:18:INFO:sort versions is [ ['1.0.0', '1.0.2', '1.0.12', '1.1.2', '1.3.3'] ].
```

参考：https://www.cnpython.com/qa/47151

## 方法三（纯数字），利用python元组的比较

```python
if __name__ == '__main__':
    v1 = ['40.1', '40.3', '40.9', '40.11', '40.10', '40.2']
    v1.sort(key=lambda x: tuple(int(v) for v in x.split(".")), reverse=True)
    print(v1)
 
    version = ['2.0.1', '1.0.2', '1.0.21', '2.2.9', '1.2.11']
    version.sort(key=lambda x: tuple(int(v) for v in x.split(".")), reverse=True)
    print(version)
    print('end!')
```
