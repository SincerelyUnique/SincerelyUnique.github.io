---
title: pandas根据A字段分组取B字段最长的那一行
date: 2019-10-11 12:23:57
tags:
- pandas
categories:
- [学习, Python语言学习, Pandas数据分析]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

![pan01](../../../../images/pan01.png)

```python
def test_for_longer():
    data = [{'foo': 'GD-20190101-010101', 'son': 'GD-123'},
            {'foo': 'GD-20190101-010101', 'son': 'GD-12345'},
            {'foo': 'GD-20190101-010102', 'son': 'GD-123'},
            {'foo': 'GD-20190101-010102', 'son': 'GD-123456'}]
    df = pd.DataFrame(data)
    df['size'] = df['son'].str.len()
    # df['size'] = df.apply(lambda x: len(x['son']), axis=1)
    idx = df.groupby('foo')['size'].idxmax()
    return df.loc[idx, ['foo', 'son']]
 
 
if __name__ == '__main__':
    val = test_for_longer()
    print(val)
```
