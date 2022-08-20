---
title: 记python2并发场景下使用strptime的问题
date: 2020-07-16 10:53:03
tags:
- concurrent
- python
categories:
- [学习, Python语言学习, Python]
---

## 报错信息

> AttributeError: 'module' object has no attribute '_strptime'

## 报错原因

页面多个ajax请求到后台，同时调用一个公共方法，公共方法内有调用

> datetime.datetime.strptime(from_date, '%Y-%m-%d')

## 问题重现及解决

```python
def parse_date1(date_str):
    """method 1: import datetime for each thread"""
    import datetime
    thread_name = threading.current_thread().getName()
    print('thread name: %s' % thread_name)
    result = datetime.datetime.strptime(date_str, '%Y-%m-%d %H:%M:%S')
    print('thread %s parse result is %s' % (thread_name, result))
 
 
c = threading.RLock()
 
 
def parse_date2(date_str):
    """method 2: with re-entrant lock"""
    with c:
        thread_name = threading.current_thread().getName()
        print('thread name: %s' % thread_name)
        result = datetime.datetime.strptime(date_str, '%Y-%m-%d %H:%M:%S')
        print('thread %s parse result is %s' % (thread_name, result))
 
 
def parse_date3(date_str):
    """method 3: import _strptime"""
    import _strptime
    thread_name = threading.current_thread().getName()
    print('thread name: %s' % thread_name)
    result = datetime.datetime.strptime(date_str, '%Y-%m-%d %H:%M:%S')
    print('thread %s parse result is %s' % (thread_name, result))
 
 
lock = threading.RLock()
 
 
def parse_date4(date_str):
    """method 4: import re-entrant lock, acquire and release"""
    lock.acquire()
    thread_name = threading.current_thread().getName()
    print('thread name: %s' % thread_name)
    result = datetime.datetime.strptime(date_str, '%Y-%m-%d %H:%M:%S')
    print('thread %s parse result is %s' % (thread_name, result))
    lock.release()
 
 
# method 5：upgrade to python3
 
 
def create_thread():
    date_str = "2020-07-16 04:23:02"
    threads = []
    for i in range(5):
        thread = threading.Thread(target=parse_date1, args=(date_str,))
        threads.append(thread)
 
    for t in threads:
        t.start()
 
    for t in threads:
        t.join()
 
    print('threads run complete.')
 
 
if __name__ == '__main__':
    create_thread()
```
