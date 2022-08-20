---
title: ElementTree合并子节点（2）
date: 2020-04-03 16:38:46
tags:
- python
- ElementTree
categories:
- [学习, Python语言学习, Python]
---

```python
def refactor_xml_param(xml_str):
    """
    Refactor xml params:
        xml_tmp1 = "<site>" \
                   "    <a>1</a>" \
                   "    <a>1</a>" \
                   "    <b>1</b>" \
                   "    <c><d>111</d></c>" \
                   "    <c><d>112</d></c>" \
                   "    <c><e>221</e></c>" \
                   "    <c><e>221</e></c>" \
                   "</site>"
        xml_tmp2 = "<site>" \
                   "    <a><m>111</m></a>" \
                   "    <a><n>111</n></a>" \
                   "    <b><k>222</k></b>" \
                   "    <q><g>111</g></q>" \
                   "    <q><d>112</d></q>" \
                   "    <q><e>221</e></q>" \
                   "    <l><o><z>111</z></o></l>" \
                   "    <l><o><h>222</h></o></l>" \
                   "    <l><o><h>222</h><p>222</p></o></l>" \
                   "</site>"
        convert xml_tmp1 to "<site><c><d>111</d><d>112</d><e>221</e></c><a>1</a><b>1</b></site>"
        convert xml_tmp2 to "<site><a><m>111</m><n>111</n></a><q><e>221</e><d>112</d><g>111</g></q>
            <l><o><p>222</p><z>111</z><h>222</h></o></l><b><k>222</k></b></site>"
    :param
    """
    root = ElementTree.fromstring(xml_str)
    # remove duplicate xml config node
    children = root.getchildren()
    children = [ElementTree.tostring(child).strip() for child in children]
    children = list(set(children))
    xml_str = '<site>' + ''.join(children) + '</site>'
    root = ElementTree.fromstring(xml_str)
    # xml tag counter, if not duplicate, return xml str directly
    parent_map = dict((c, p) for p in root.getiterator() for c in p)
    nodes = [(node.tag, node, parent_map[node]) for node in root.iter() if node is not root]
    tags = [n[0] for n in nodes]
    counter = dict(Counter(tags))
    duplicates = [key for key, value in counter.items() if value > 1]
    if not duplicates:
        xml_str = ElementTree.tostring(root)
        print(xml_str)
        return xml_str
    # if xml tag duplicate, make new xml str
    new_xml = make_new_xml(root, Element('site'), Element('site'))
    xml_str = ElementTree.tostring(new_xml)
    print(xml_str)
    return xml_str
 
 
def make_new_xml(root, element, new_root):
    """
    Make a new xml element:
    :param root: Element(has old children, the children maybe changed after processing), merge duplicate tag
    :param element: Element(no children), storage new children what after root processing temporarily
    :param new_root: Element(has old children), storage new children, and changed every time in stack
    :return new_root: An element with full sub element which after processing
    """
    # remove duplicate xml node(Not used for the first time)
    children = root.getchildren()
    children = [ElementTree.tostring(child).strip() for child in children]
    children = list(set(children))
    xml_str = '<' + element.tag + '>' + ''.join(children) + '</' + element.tag + '>'
    root = ElementTree.fromstring(xml_str)
 
    # xml tag counter
    level_nodes = [first for first in root.getchildren()]
    level_tags = [level_node.tag for level_node in level_nodes]
    level_counter = dict(Counter(level_tags))
 
    # if tag duplicate, create new sub element, and move old sub elements' children to new sub element
    level_duplicates = [key for key, value in level_counter.items() if value > 1]
    for dup in level_duplicates:
        cur_dup_cases = root.findall(dup)
        child = SubElement(element, dup)
        for case in cur_dup_cases:
            case_children = case.getchildren()
            for case_child in case_children:
                child.append(case_child)
 
    # if tag not duplicate, move old sub element to new sub element directly
    level_not_duplicates = [key for key, value in level_counter.items() if value == 1]
    for not_dup in level_not_duplicates:
        cur_not_dup_case = root.find(not_dup)
        element.append(cur_not_dup_case)
    new_root_children = element.getchildren()
 
    # remove old sub element from parent node(old sub elements maybe duplicate)
    n_children = new_root.getchildren()
    for n_child in list(n_children):
        new_root.remove(n_child)
 
    if new_root_children > 0:
        # add new sub element to parent node
        for child in new_root_children:
            new_root.append(child)
        # traverse each new sub element, because of new sub element's sub element maybe also duplicate
        for child in new_root_children:
            # when node meets ['c'], do not need merge their duplicate nodes
            # when new sub element has no child, do not need next step
            if child.tag not in ['c'] and len(child.getchildren()) > 0:
                make_new_xml(child, Element(child.tag), new_root.find(child.tag))
    return new_root
 
 
if __name__ == '__main__':
    xml_tmp1 = "<site>" \
               "    <a>1</a>" \
               "    <a>1</a>" \
               "    <b>1</b>" \
               "    <c><d>111</d></c>" \
               "    <c><d>112</d></c>" \
               "    <c><e>221</e></c>" \
               "    <c><e>221</e></c>" \
               "</site>"
    xml_tmp2 = "<site>" \
               "    <a><m>111</m></a>" \
               "    <a><n>111</n></a>" \
               "    <b><k>222</k></b>" \
               "    <q><g>111</g></q>" \
               "    <q><d>112</d></q>" \
               "    <q><e>221</e></q>" \
               "    <l><o><z>111</z></o></l>" \
               "    <l><o><h>222</h></o></l>" \
               "    <l><o><h>222</h><p>222</p></o></l>" \
               "</site>"
    res = refactor_xml_param(xml_tmp2)
```
