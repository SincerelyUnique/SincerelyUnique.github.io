---
title: python安装gutenberg（windows）
date: 2020-12-30 11:55:41
tags:
- python
categories:
---

```
python安装gutenberg
 
这里是用pycharm terminal安装（仅供参考）
 
 
1.直接easy_install报错
(venv) C:\Users\jalchu\test>easy_install Gutenberg
WARNING: The easy_install command is deprecated and will be removed in a future version.
Searching for Gutenberg
Reading https://pypi.org/simple/Gutenberg/
Downloading https://files.pythonhosted.org/packages/33/99/xxx
... ...
Installed c:\users\jalchu\py38_env\venv\lib\site-packages\rdflib_sqlalchemy-0.4.0-py3.8.egg
Searching for bsddb3>=6.1.0
Reading https://pypi.org/simple/bsddb3/
Downloading https://files.pythonhosted.org/packages/f0/24/bsddb3-6.2.9.tar.gz#sha25
6=70d05ec8dc568f42e70fc919a442e0daadc2a905a1cfb7ca77f549d49d6e7801
Best match: bsddb3 6.2.9
Processing bsddb3-6.2.9.tar.gz
Writing C:\Users\jalchu\AppData\Local\Temp\easy_install-crd1q0t7\bsddb3-6.2.9\setup.cfg
Running bsddb3-6.2.9\setup.py -q bdist_egg --dist-dir C:\Users\jalchu\AppData\Local\Temp\easy_install-crd1q0t7\bsddb3-6.2.9\egg-dist-tmp-3jv21bw
4
error: [Errno 2] No such file or directory: 'db/include\\db.h'
 
 
 
2.解决
下载windows CPython预先构建好的、扩展的二进制安装包，bsddb3可以从下面地址下载
下载地址：https://www.lfd.uci.edu/~gohlke/pythonlibs
这个网站提供了windows 32-bit and 64-bit二进制开源扩展包，主要针对官方CPython，部分用于PYPY。
 
注意：
这些包不是官方的：即非正式的，无法识别的，个人的，不受支持的，没有保护的，且对使用是不负责任的；
安装注意根据python的版本(cp38=CPython解释器，版本为3.8)和处理器架构（win_amd64 or win32）来选择。
选错了没关系，安装时会有提示的。
 
 
（1）下载whl到本地，安装
(venv) C:\Users\jalchu\test>easy_install C:\Users\jalchu\Downloads\bsddb3-6.2.9-cp38-cp38-win_amd64.whl
WARNING: The easy_install command is deprecated and will be removed in a future version.
Processing bsddb3-6.2.9-cp38-cp38-win_amd64.whl
Installing bsddb3-6.2.9-cp38-cp38-win_amd64.whl to c:\users\jalchu\py38_env\venv\lib\site-packages
Adding bsddb3 6.2.9 to easy-install.pth file
 
Installed c:\users\jalchu\py38_env\venv\lib\site-packages\bsddb3-6.2.9-py3.8-win-amd64.egg
Processing dependencies for bsddb3==6.2.9
Finished processing dependencies for bsddb3==6.2.9
 
（2）尝试安装gutenberg报Access is denied
(venv) C:\Users\jalchu\test>easy_install gutenberg
WARNING: The easy_install command is deprecated and will be removed in a future version.
Searching for gutenberg
Best match: gutenberg 0.8.1
Processing gutenberg-0.8.1-py3.8.egg
gutenberg 0.8.1 is already the active version in easy-install.pth
 
Using c:\users\jalchu\py38_env\venv\lib\site-packages\gutenberg-0.8.1-py3.8.egg
Processing dependencies for gutenberg
Searching for SPARQLWrapper>=1.8.2
Reading https://pypi.org/simple/SPARQLWrapper/
Downloading https://files.pythonhosted.org/packages/00/9b/SPARQLWrapper-1.8.5-py3-n
one-any.whl#sha256=c7f9c9d8ebb13428771bc3b6dee54197422507dcc3dea34e30d5dcfc53478dec
Best match: SPARQLWrapper 1.8.5
Processing SPARQLWrapper-1.8.5-py3-none-any.whl
Installing SPARQLWrapper-1.8.5-py3-none-any.whl to c:\users\jalchu\py38_env\venv\lib\site-packages
error: [WinError 5] Access is denied: 'c:\\users\\jalchu\\\xxx\\test\\py38_env\\venv\\lib\\site-packages\\SPARQLWrapp
er-1.8.5-py3.8.egg\\SPARQLWrapper-1.8.5.dist-info' -> 'c:\\users\\jalchu\\\xxx\\test\\py38_env\\venv\\lib\\site-packa
ges\\SPARQLWrapper-1.8.5-py3.8.egg\\EGG-INFO'
 
 
（3）使用python -m安装，安装成功，重启pycharm
(venv) C:\Users\jalchu\test>python -m pip install gutenberg
Requirement already satisfied: gutenberg in c:\users\jalchu\py38_env\venv\lib\site-packages\gutenberg-0.8.1-
py3.8.egg (0.8.1)
... ...
Requirement already satisfied: MarkupSafe>=0.9.2 in c:\users\jalchu\py38_env\venv\lib\site-packages (from Ma
ko->alembic>=0.8.8->rdflib-sqlalchemy>=0.3.8->gutenberg) (1.0)
Installing collected packages: SPARQLWrapper, isodate
Successfully installed SPARQLWrapper-1.8.5 isodate-0.6.0
 
 
 
参考：
https://stackoverflow.com/questions/33714698/installing-bsddb3-6-1-1-in-windows-filenotfounderror-db-include-db-h
 
 
 
备注：
1.python解释器
CPython是用C语言实现的Python解释器。作为官方实现，它是最广泛使用的Python解释器。
此外还有用Java实现的Jython，用.NET实现的IronPython等。
 
CPython有个并发上的问题，即在多处理器的计算机上使用CPython的主要问题来自CPython的全局解释
器锁（Global Interpreter Lock，GIL），它使得CPython不能进行并发编程。
要做到并发编程，就必须为每一个线程运行一个解释器。如果这样的话，它们之间的通讯就非常困难。
所以社区内一直在讨论是否要从CPython中去除GIL。
 
除了CPython解释器，还有IPython解释器。
IPython是一种基于CPython之上交互式解释器。相较于本地的Python Shell，IPython提供了更为强大的
编辑和交互功能，它拥有一套复杂的并行和分配计算结构。对于GIL是一个很好的弥补。
 
PyPy是另一个Python解释器，它的目标是执行速度，采用JIT技术，对Python代码进行动态编译。
```
