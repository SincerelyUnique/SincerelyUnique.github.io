---
title: python计算两日期之间工作日时长
date: 2019-04-25 17:27:33
tags:
- python
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 原因：使用dateutil的rrule时，计算速度比较慢

```python
def axx():
    from dateutil import rrule
    received_time = datetime.datetime.strptime('2019-04-21 23:00:00', '%Y-%m-%d %H:%M:%S')
    complete_time = datetime.datetime.strptime('2019-04-22 01:00:00', '%Y-%m-%d %H:%M:%S')
    workdays = [x for x in range(7) if x not in [5, 6]]
    time_period = rrule.rrule(rrule.MINUTELY, dtstart=received_time, until=complete_time, byweekday=workdays).count()
    print(time_period)
```

2. 尝试使用pandas的bdate_range，但是发现只统计工作日天数，即便不足1天也是按1天算的，不符合需求，因为我要分钟

```python
def xxa():
    import pandas as pd
    date = pd.bdate_range('2019-04-21 23:00:00', '2019-04-22 01:00:00', freq='min')
    minutes = len(date)  
    print(minutes)
    print(minutes/(60*60))
```

3. 从stackoverflow找到一个方法

```python
def xax():
    from business_duration import businessDuration
    import pandas as pd
    received_time = pd.to_datetime('2019-04-21 23:00:00')
    complete_time = pd.to_datetime('2019-04-22 01:15:00')
    period = businessDuration(received_time, complete_time, unit='min')
    print(period)
```

4. 自己使用pandas写的，还需测试

```python
def aaa():
    import pandas as pd
    # test case 1
    # received_time = '2019-04-21 23:00:00'
    # complete_time = '2019-04-22 01:00:00'
    # received_time = '2019-04-19 23:00:00'
    # complete_time = '2019-04-20 01:00:00'
 
    # test case 2
    # received_time = '2019-04-18 23:00:00'
    # complete_time = '2019-04-20 01:00:00'
    # received_time = '2019-04-21 23:00:00'
    # complete_time = '2019-04-23 01:00:00'
 
    # test case 3
    # received_time = '2019-04-21 23:00:00'
    # complete_time = '2019-04-24 01:00:00'
    # received_time = '2019-04-18 23:00:00'
    # complete_time = '2019-04-20 01:00:00'
 
    # test case 5
    received_time = '2019-04-19 23:00:00'
    complete_time = '2019-04-22 01:00:00'
    received_date = pd.to_datetime(received_time)
    complete_date = pd.to_datetime(complete_time)
    date_period = pd.bdate_range(received_time, complete_time)
 
    if date_period[0] == date_period[-1]:
        if date_period[0] > received_date:
            start = date_period[0]
            end = complete_date
        else:
            start = received_date
            end = date_period[0] + datetime.timedelta(days=1)
        day_time = len(pd.date_range(start, end, freq='min')) - 1
        print('Workdays:' + str(day_time) + ' minutes')
    else:
        if (complete_date - date_period[-1]).days > 0:
            end = date_period[-1] + datetime.timedelta(days=1)
        else:
            end = complete_date
        if received_date < date_period[0]:
            start = date_period[0]
        else:
            start = received_date
        received_per = pd.date_range(start, date_period[0] + datetime.timedelta(days=1), freq='min')
        complete_per = pd.date_range(date_period[-1], end, freq='min')
        middle_time = (len(date_period) - 2) * 1440
        days_time = len(received_per) + middle_time + len(complete_per) - 2
        print('Workdays:' + str(days_time) + ' minutes')
```

参考：https://stackoverflow.com/questions/46899627/business-hours-between-two-dates-in-pandas-dataframe-including-holidays?rq=1
