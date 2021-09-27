---
title: python获取PST(太平洋)时间
date: 2020-07-10 10:22:05
tags:
- python
categories:
---

PST时间比较特殊，存在夏令时和冬令时，夏令时，又称“日光节约时制”（Daylight Saving Time），是一种为节约能源而人为规定地方时间的制度，在这一制度实行期间所采用的统一时间称为“夏令时”。一般在天亮早的夏季人为将时间提前一小时（或者说将时钟拨快1个小时，以美国为例，美国的标准时区是西八区，标准时间应是GMT-8，但由于此时是夏令时，美国时间就是GMT-7），可以使人早起早睡，减少照明量，以充分利用光照资源，从而节约照明用电。各个采纳夏时制的国家具体规定不同。全世界有近110个国家每年要实行夏令时。而冬令时会将时钟调整为原来标准时间（即时钟重新拨回1小时，此时美国时间就是标准的GMT-8）

```
from datetime import datetime
 
from pytz import utc, timezone
 
 
def get_current_pst_time():
    """
    获取当前时间的太平洋时间（可能是夏令时，也可能是冬令时）
    """
    print('------------(1) Current time to PST time----------------')
    local_time = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
    utc_time = datetime.now(tz=utc).strftime('%Y-%m-%d %H:%M:%S')
    pst_time = datetime.now(tz=utc).astimezone(timezone('US/Pacific')).strftime('%Y-%m-%d %H:%M:%S')
    is_summary_time = bool(datetime.now(tz=utc).astimezone(timezone('US/Pacific')).dst())
    print('is it a summary time? %s.' % is_summary_time)
    print('local time is %s.' % local_time)
    print('utc time is %s.' % utc_time)
    print('pst time is %s.' % pst_time)
 
 
def convert_pst_time_to_utc_time(pst_time_str):
    print('------------(2) PST time to UTC time----------------')
    print('pst time is %s.' % pst_time_str)
    temp_time = datetime.strptime(pst_time_str, '%Y-%m-%d %H:%M:%S')
    pacific_timezone = timezone('US/Pacific')
    pst_time = pacific_timezone.localize(temp_time, is_dst=None)
    assert pst_time.tzinfo is not None
    assert pst_time.tzinfo.utcoffset(pst_time) is not None
    is_summary_time = bool(pst_time.dst())
    print('is it a summary time? %s.' % is_summary_time)
    utc_time = pst_time.astimezone(timezone('utc'))
    print('utc time is %s.' % utc_time.strftime('%Y-%m-%d %H:%M:%S'))
 
 
def convert_utc_time_to_pst_time(utc_time_str):
    print('------------(3) UTC time to PST time----------------')
    print('utc time is %s.' % utc_time_str)
    temp_time = datetime.strptime(utc_time_str, '%Y-%m-%d %H:%M:%S')
    utc_timezone = timezone('utc')
    utc_time = utc_timezone.localize(temp_time, is_dst=None)
    assert utc_time.tzinfo is not None
    assert utc_time.tzinfo.utcoffset(utc_time) is not None
    pst_time = utc_time.astimezone(timezone('US/Pacific'))
    is_summary_time = bool(pst_time.dst())
    print('is it a summary time? %s.' % is_summary_time)
    print('pst time is %s.' % pst_time.strftime('%Y-%m-%d %H:%M:%S'))
 
 
if __name__ == '__main__':
    get_current_pst_time()
    convert_pst_time_to_utc_time('2019-12-03 02:00:00')  # pst冬令时转utc
    convert_pst_time_to_utc_time('2020-07-03 02:00:00')  # pst夏令时转utc
 
    convert_utc_time_to_pst_time('2019-12-03 10:00:00')  # utc转pst冬令时
    convert_utc_time_to_pst_time('2020-07-03 09:00:00')  # utc转pst夏令时
```

```
------------(1) Current time to PST time----------------
is it a summary time? True.
local time is 2020-07-13 21:50:34.
utc time is 2020-07-13 13:50:34.
pst time is 2020-07-13 06:50:34.
 
------------(2) PST time to UTC time----------------
pst time is 2019-12-03 02:00:00.
is it a summary time? False.
utc time is 2019-12-03 10:00:00.
 
------------(2) PST time to UTC time----------------
pst time is 2020-07-03 02:00:00.
is it a summary time? True.
utc time is 2020-07-03 09:00:00.
 
------------(3) UTC time to PST time----------------
utc time is 2019-12-03 10:00:00.
is it a summary time? False.
pst time is 2019-12-03 02:00:00.
 
------------(3) UTC time to PST time----------------
utc time is 2020-07-03 09:00:00.
is it a summary time? True.
pst time is 2020-07-03 02:00:00.
```
