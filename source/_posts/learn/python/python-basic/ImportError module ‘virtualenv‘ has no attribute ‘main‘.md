---
title: ImportError - module ‘virtualenv‘ has no attribute ‘main‘
date: 2021-06-12 09:32:35
tags:
- linux
- python
categories:
- [学习, Python语言学习, Python]
---

背景：OS是ubuntu18，python版本是Python3.6.x，执行

```shell script
virtualenv -p python venv
```

创建虚拟环境时报

```shell script
❯ virtualenv -p python venv
Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/pkg_resources/__init__.py", line 2458, in resolve
    return functools.reduce(getattr, self.attrs, module)
AttributeError: module 'virtualenv' has no attribute 'main'
 
The above exception was the direct cause of the following exception:
 
Traceback (most recent call last):
  File "/usr/bin/virtualenv", line 11, in <module>
    load_entry_point('virtualenv==15.1.0', 'console_scripts', 'virtualenv')()
  File "/usr/lib/python3/dist-packages/pkg_resources/__init__.py", line 474, in load_entry_point
    return get_distribution(dist).load_entry_point(group, name)
  File "/usr/lib/python3/dist-packages/pkg_resources/__init__.py", line 2846, in load_entry_point
    return ep.load()
  File "/usr/lib/python3/dist-packages/pkg_resources/__init__.py", line 2450, in load
    return self.resolve()
  File "/usr/lib/python3/dist-packages/pkg_resources/__init__.py", line 2460, in resolve
    raise ImportError(str(exc)) from exc
ImportError: module 'virtualenv' has no attribute 'main'
```

换了另外一种方式创建虚拟环境（其实某种程度上是回避了上面的问题，以后有空可以再查下）

```shell script
> sudo apt install python3-venv
> python3 -m venv venv
```

参考：https://www.reddit.com/r/archlinux/comments/fhkk9s/attributeerror_module_virtualenv_has_no_attribute/
