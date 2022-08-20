---
title: pandas一行拆分成多行
date: 2019-06-26 16:13:38
tags:
- python
- pandas
categories:
- [学习, Python语言学习, Pandas数据分析]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

（1） 根据某一列拆分

效果：

![split01](../../../../images/split01.png)

![split02](../../../../images/split02.png)

代码(注意index，如果index有重复会有问题，最好先reset_index)：

```python
if __name__ == '__main__':
    import pandas as pd
    df = pd.DataFrame({'Country': ['China,US', 'Japan,EU', 'UK,Australia', 'Singapore,Netherland'],
                       'Number': [100, 150, 120, 90],
                       'Value': [1, 2, 3, 4],
                       'label': list('abcd')})
    df_split_row = df.drop('Country', axis=1).join(
        df['Country'].str.split(',', expand=True).stack().reset_index(level=1, drop=True).rename('Country'))
```

（2） 根据多列拆分

效果：

![split03](../../../../images/split03.png)

![split04](../../../../images/split04.png)

代码：

```python
if __name__ == '__main__':
    import pandas as pd
    df = pd.DataFrame({'Code': ['212027'],
                       'Color': ['blue, pink, yellow'],
                       'Size': ['12-18M, 2-3Y, 3-4Y']})
    split_columns = ['Color', 'Size']
    df_convert = df.drop(columns=split_columns, axis=1)
    for column in split_columns:
        df_convert = df_convert.join(
            df[column].str.split(',', expand=True).stack().reset_index(level=1, drop=True).rename(column))
    df_convert = df_convert.reset_index(drop=True)
```
