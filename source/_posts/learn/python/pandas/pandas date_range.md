---
title: pandas date_range
date: 2019-09-09 11:24:08
tags:
- pandas
categories:
- [学习, Python语言学习, Pandas数据分析]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
def get_date_range():
    from_date_str, to_date_str = '2019-08-01', '2019-09-09'
    df_date_range = pd.date_range(from_date_str, to_date_str)
    df = pd.DataFrame(df_date_range, columns=['Date'])
    df['Date'] = df.apply(lambda x: x['Date'].strftime("%Y-%m-%d"), axis=1)
 
    from_date_str, to_date_str = '2019-08-01', '2019-09-09'
    df_date_range = pd.date_range(from_date_str, to_date_str, freq='MS')
    df = pd.DataFrame(df_date_range, columns=['Date'])
    df['Date'] = df.apply(lambda x: x['Date'].strftime("%Y-%m"), axis=1)
 
    from_date_str, to_date_str = '2019-08-01', '2019-09-09'
    df = pd.DataFrame()
    df['monday'] = pd.date_range(from_date_str, to_date_str, freq='W-MON')
    df['sunday'] = pd.date_range(from_date_str, to_date_str, freq='W-SUN')
    df['monday'] = df.apply(lambda x: x['monday'].strftime("%Y-%m-%d"), axis=1)
    df['sunday'] = df.apply(lambda x: x['sunday'].strftime("%Y-%m-%d"), axis=1)
 
 
if __name__ == '__main__':
    get_date_range()
```

参考：

https://stackoverflow.com/questions/53844922/pandas-date-range-on-a-weekly-basis-starting-with-a-particular-day-of-the-week

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.date_range.html
