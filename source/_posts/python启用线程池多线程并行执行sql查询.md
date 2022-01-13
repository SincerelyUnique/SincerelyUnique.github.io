---
title: python启用线程池多线程并行执行sql查询
date: 2022-01-13 14:34:43
tags:
categories: python
---

> 背景：有一个请求，后端有很多同步执行的sql，速度比较慢，这里尝试使用concurrent.futures包下的ThreadPoolExecutor，创建一个线程池，然后分配线程同时执行这些sql，最后获取结果

## 说明
需要注意的是Python由于GIL限制，其实这里的并行是伪并行，因为GIL锁限制进程内的线程只能独享cpu，并不能利用多核优势，但是由于我们是io密集型程序，GIL在线程IO时会释放cpu资源同时做线程上下文切换，所以基本可以忽略GIL锁对这里使用多线程的影响。

另外其实有个好一些的方法，就是使用python3.4后新增功能-协程，方法使用async修饰，配合await，能够实现单线程高吞吐量，但是改动比较大，并且底层orm需要再次封装，暂时先不考虑。

暂且先使用多线程。

```python
def query_user():
    print(datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"))
    sql = "select * from USER"
    return pd.read_sql_query(sql, engine)


def query_test(name, age):
    print(datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"))
    sql = "select * from TEST"
    return pd.read_sql_query(sql, engine)


def query_info(name):
    print(datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"))
    sql = "select * from INFO"
    return pd.read_sql_query(sql, engine)


def query_rule(name=None, type=None):
    print(datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"))
    sql = "select * from INFO"
    return pd.read_sql_query(sql, engine)


def concurrent_test():
    with concurrent.futures.ThreadPoolExecutor() as executor:
        futures = [executor.submit(query_user),
                   executor.submit(query_test, *['Jalen', 12]),
                   executor.submit(query_rule, **{'type': 'commonRule'}),
                   executor.submit(query_info, 'Jalen')]
        user_value = futures[0].result()
        test_value = futures[1].result()
        rule_value = futures[2].result()
        info_value = futures[3].result()
        print(user_value)
        print(test_value)
        print(rule_value)
        print(info_value)
    print('End!')


if __name__ == '__main__':
    concurrent_test()
    print('End!')
```
