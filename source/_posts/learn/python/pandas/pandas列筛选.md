---
title: pandas列筛选
date: 2019-08-08 10:36:14
tags:
- pandas
categories:
- [学习, Python语言学习, Pandas数据分析]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
def pandas_test():
    """
    (1) str.contains -> 字符串包含
    (2) isin -> 列表包含
    :return:
    """
    temp = [{'name': 'cat', 'age': 12},
            {'name': 'pig', 'age': 13},
            {'name': 'mouse', 'age': 10},
            {'name': 'monkey', 'age': 20}]
    df = pd.DataFrame(temp)
    df1 = df[df['name'].str.contains('mo')]  # 筛选出name这一列中包含mo的所有行
    df2 = df[~df['name'].str.contains('mo')]  # 筛选出name这一列中不包含mo的所有行
    df3 = df[df.name.isin(['mouse', 'chick', 'duck'])]  # 筛选出name这一列存在于指定列表的所有行
    df4 = df[~df.name.isin(['mouse', 'chick', 'duck'])]  # 筛选出name这一列不存在于指定列表的所有行
    print(df1)
    print(df2)
    print(df3)
    print(df4)
 
 
if __name__ == '__main__':
    pandas_test()
```
