---
title: Pandas多行并一行
date: 2020-11-23 14:39:17
tags:
- python
- pandas
categories:
- [学习, Python语言学习, Pandas]
---

一行拆多行参考：https://blog.csdn.net/baidu_30809315/article/details/93748732

![pandas01](../../../../images/pandas01.png)

![pandas02](../../../../images/pandas02.png)

```python
def merge_rows_in_pandas():
    data = [{'name': 'jalen', 'age': 12, 'love': 'basketball'},
            {'name': 'jalen', 'age': 12, 'love': 'football'},
            {'name': 'kitty', 'age': 20, 'love': 'movies'},
            {'name': 'kitty', 'age': 20, 'love': 'singing'}]
    df = pd.DataFrame(data)
    df['love'] = df.groupby(['name', 'age'], as_index=False)['love'].transform(lambda x: ','.join(x))
    df.drop_duplicates(subset=['name', 'age'], keep='first', inplace=True)
 
 
if __name__ == '__main__':
    merge_rows_in_pandas()
    print('end!')
```
