---
title: ElementTree合并子节点（1）
date: 2019-12-09 01:20:34
tags:
- python
- xml
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

例如：

```xml
<site> 
  <tools> 
    <name>jalen.site</name> 
  </tools>  
  <tools> 
    <server>aliyun</server> 
  </tools>
</site>
```

合并为：

```xml
<site> 
  <tools>
    <name>jalen.site</name>  
    <server>aliyun</server> 
  </tools>
</site>
```

代码：

```python
from xml.etree import ElementTree
from xml.etree.ElementTree import SubElement
from collections import Counter
 
 
def test_et(xml_str):
    root = ElementTree.fromstring(xml_str)
    parent_map = dict((c, p) for p in root.getiterator() for c in p)
    nodes = [(node.tag, parent_map[node]) for node in root.iter() if node is not root]
    counter = dict(Counter(nodes))
    duplicates = [key for key, value in counter.items()if value > 1]
    if not duplicates:
        return ElementTree.tostring(root)
    for node in duplicates:
        elements = [neighbor for neighbor in root.iter(node[0])]
        elements = [element for element in elements if parent_map[element] == node[1]]
        new = SubElement(node[1], node[0])
        for ele in elements:
            old = [elem for elem in ele.iter() if elem is not ele]
            node[1].remove(ele)
            new.extend(old)
    return ElementTree.tostring(root)
 
 
if __name__ == '__main__':
    xml_str = "<site>" \
              "     <tools>" \
              "         <name>jalen.site</name>" \
              "     </tools>" \
              "     <tools>" \
              "         <server>aliyun</server>" \
              "     </tools>" \
              "</site>"
    res = test_et(xml_str)
    print(res)
 
    xml_str = "<site>" \
              "     <tools>" \
              "         <name>jalen.site</name>" \
              "     </tools>" \
              "     <tools>" \
              "         <server>aliyun1</server>" \
              "     </tools>" \
              "     <tools>" \
              "         <server>aliyun2</server>" \
              "     </tools>" \
              "     <tools>" \
              "         <server>aliyun3</server>" \
              "     </tools>" \
              "</site>"
    res = test_et(xml_str)
    print(res)
 
    xml_str = "<site>" \
              "     <tools>" \
              "         <name>jalen.site</name>" \
              "     </tools>" \
              "     <tools>" \
              "         <server>aliyun1</server>" \
              "     </tools>" \
              "     <operate>" \
              "         <server>aliyun2</server>" \
              "     </operate>" \
              "     <operate>" \
              "         <server>aliyun3</server>" \
              "     </operate>" \
              "</site>"
    res = test_et(xml_str)
    print(res)
 
    xml_str = "<site>" \
              "     <tools>" \
              "         <name>jalen.site</name>" \
              "     </tools>" \
              "     <tools>" \
              "         <server>aliyun1</server>" \
              "     </tools>" \
              "     <operate>" \
              "         <test><name>kite.site</name></test>" \
              "         <test><server>aliyun2</server></test>" \
              "     </operate>" \
              "</site>"
    res = test_et(xml_str)
    print(res)
```

参考：

https://docs.python.org/2/library/xml.etree.elementtree.html

https://stackoverflow.com/questions/10408927/how-to-get-all-sub-elements-of-an-element-tree-with-python-elementtree

https://blog.csdn.net/August1226/article/details/82144759

https://stackoverflow.com/questions/37335943/removing-xml-subelement-tags-with-python-using-elementtree-and-remove

https://codeday.me/bug/20171012/84502.html
