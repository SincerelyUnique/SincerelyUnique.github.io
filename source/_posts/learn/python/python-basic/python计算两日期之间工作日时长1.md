---
title: python计算两日期之间工作日时长
date: 2019-04-22 14:11:30
tags:
- python
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 代码

```python
def exclude_weekend_time():
    from dateutil import rrule
 
    start_date = datetime.datetime.strptime('2019-04-19 13:21:46', '%Y-%m-%d %H:%M:%S')
    end_date = datetime.datetime.strptime('2019-04-22 13:21:46', '%Y-%m-%d %H:%M:%S')
 
    days_off = 5, 6
    workdays = [x for x in range(7) if x not in days_off]
    secondly = rrule.rrule(rrule.SECONDLY, dtstart=start_date, until=end_date, byweekday=workdays)
    seconds = secondly.count()
    print(seconds)
 
 
if __name__ == '__main__':
    exclude_weekend_time()
```

2. 如果计算相隔年，或相隔月份，相隔天数，可以修改规则

```
# 间隔秒数
secondly = rrule.rrule(rrule.SECONDLY, dtstart=start_date, until=end_date, byweekday=workdays)
 
# 间隔分钟数
secondly = rrule.rrule(rrule.MINUTELY, dtstart=start_date, until=end_date, byweekday=workdays)
 
# 间隔小时数
secondly = rrule.rrule(rrule.HOURLY, dtstart=start_date, until=end_date, byweekday=workdays)
 
# 间隔天数
secondly = rrule.rrule(rrule.DAILY, dtstart=start_date, until=end_date, byweekday=workdays)
 
... ...
```

参考：https://blog.csdn.net/houyj1986/article/details/21880331
