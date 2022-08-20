---
title: python同级xml节点值比较
date: 2019-12-12 16:41:01
tags:
- python
- xml
categories:
- [学习, Python语言学习, Python]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> 背景：给定两个xml字符串，然后比较两个xml相同层级、相同名字（在ElementTree组建里叫tag）的节点的值是否一致，相同层级不能含有多个名字相同的子节点（即tag不能重复），如果发现有值不一致的，返回True

```python
def compare_xml(left_nodes, right_nodes, key_info='.'):
    left_len, right_len = len(left_nodes), len(right_nodes)
    for i in range(left_len):
        left_node_tag, left_node_text = left_nodes[i].tag, left_nodes[i].text
        for j in range(right_len):
            right_node_tag, right_node_text = right_nodes[j].tag, right_nodes[j].text
            if right_node_tag == left_node_tag:
                if right_node_text == left_node_text:
                    s = '%s/%s' % (key_info, left_node_tag)
                    if len(left_nodes[i].getchildren()) > 0:
                        return compare_xml(left_nodes[i], right_nodes[j], s)
                else:
                    s = '%s/%s' % (key_info, left_node_tag)
                    print(s)
                    return True
 
 
if __name__ == '__main__':
    xml_str1 = "<site>" \
               "     <tools>" \
               "         <name>jalen.site</name>" \
               "     </tools>" \
               "     <metaData>" \
               "         <server>aliyun</server>" \
               "     </metaData>" \
               "</site>"
    xml_str2 = "<site>" \
               "     <metaData>" \
               "         <server><name>jalen.site11</name></server>" \
               "     </metaData>" \
               "     <tools>" \
               "         <name>jalen.site</name>" \
               "     </tools>" \
               "</site>"
    left_root = ElementTree.fromstring(xml_str1)
    right_root = ElementTree.fromstring(xml_str2)
    left_nodes = left_root.getchildren()
    right_nodes = right_root.getchildren()
    res = compare_xml(left_nodes, right_nodes)
    if res:
        print('difference.')
    else:
        print('same.')
```
