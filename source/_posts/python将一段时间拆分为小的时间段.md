---
title: python将一段时间拆分为小的时间段
date: 2019-11-12 11:31:04
tags:
- python
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
import pandas as pd
 
 
def split_time_ranges(from_time, to_time, frequency):
    from_time, to_time = pd.to_datetime(from_time), pd.to_datetime(to_time)
    time_range = list(pd.date_range(from_time, to_time, freq='%sS' % frequency))
    if to_time not in time_range:
        time_range.append(to_time)
    time_range = [item.strftime("%Y-%m-%d %H:%M:%S") for item in time_range]
    time_ranges = []
    for item in time_range:
        f_time = item
        t_time = (datetime.datetime.strptime(item, "%Y-%m-%d %H:%M:%S") + datetime.timedelta(seconds=frequency))
        if t_time >= to_time:
            t_time = to_time.strftime("%Y-%m-%d %H:%M:%S")
            time_ranges.append([f_time, t_time])
            break
        time_ranges.append([f_time, t_time.strftime("%Y-%m-%d %H:%M:%S")])
    return time_ranges
 
 
if __name__ == '__main__':
    from_time = '2019-10-01 10:20:45'
    to_time = '2019-10-04 10:20:45'
    frequency = 60 * 60 * 5
    time_ranges = split_time_ranges(from_time, to_time, frequency)
    print(time_ranges)
```

输出

```
[
['2019-10-01 10:20:45', '2019-10-01 15:20:45'], 
['2019-10-01 15:20:45', '2019-10-01 20:20:45'], 
['2019-10-01 20:20:45', '2019-10-02 01:20:45'], 
['2019-10-02 01:20:45', '2019-10-02 06:20:45'], 
['2019-10-02 06:20:45', '2019-10-02 11:20:45'], 
['2019-10-02 11:20:45', '2019-10-02 16:20:45'], 
['2019-10-02 16:20:45', '2019-10-02 21:20:45'], 
['2019-10-02 21:20:45', '2019-10-03 02:20:45'], 
['2019-10-03 02:20:45', '2019-10-03 07:20:45'], 
['2019-10-03 07:20:45', '2019-10-03 12:20:45'], 
['2019-10-03 12:20:45', '2019-10-03 17:20:45'], 
['2019-10-03 17:20:45', '2019-10-03 22:20:45'], 
['2019-10-03 22:20:45', '2019-10-04 03:20:45'], 
['2019-10-04 03:20:45', '2019-10-04 08:20:45'], 
['2019-10-04 08:20:45', '2019-10-04 10:20:45']
]
```
