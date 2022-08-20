---
title: Grafana表格添加dashboard link
date: 2020-12-02 12:39:17
tags:
- grafana
- dashboard
categories:
- [学习, DevOPS, Grafana]
---

为grafana里的表格（table-cell）添加dashboard link

这样点击其中某个表格时会自动跳转到另外一个dashboard

grafana版本：v7.1.1

（1）edit panel(table) ->  Field -> Data Links -> add link

   （link指向另一个dashboard，此时table整行每个cell都含有该link）
   
（2）edit panel(table) ->  Field -> Overrides -> add override(选择Filter by field及想要添加link的那一列field名字) -> add override property（选择Data Link）-> add link

   （link指向另一个dashboard，此时table每行只有被添加的field那一列才含有该link，即仅某一列显示link）

![grafana01](../../../../images/grafana01.gif)

![grafana02](../../../../images/grafana02.gif)

参考：

https://grafana.com/docs/grafana/latest/linking/data-links/

https://grafana.com/docs/grafana/latest/linking/data-link-variables/
