---
title: pandas找出比较两列并找出不同的值
date: 2019-08-02 14:44:34
tags:
- pandas
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```python
def compare_site_names():
    df1 = pd.read_csv(r'C:\Users\jalchu\Desktop\reservedSiteNames1.txt', header=None)
    df2 = pd.read_csv(r'C:\Users\jalchu\Desktop\reservedSiteNames2.txt', header=None)
    data1 = df1.to_dict(orient='list')[0]
    data2 = df2.to_dict(orient='list')[0]
    diff1 = set(data1).difference(data2)
    diff2 = set(data2).difference(data1)
    print(diff1)
    print(diff2)
 
 
if __name__ == '__main__':
    compare_site_names()
```
