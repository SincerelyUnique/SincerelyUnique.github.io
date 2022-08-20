---
title: Liferay通过layout获取portletid
date: 2017-02-20 15:51:03
tags: liferay
categories:
- [学习, Java语言学习, Liferay]
---
```
Layout layout = LayoutLocalServiceUtil.getLayout(plid);
LayoutTypePortlet layoutTypePortlet = (LayoutTypePortlet)layout.getLayoutType();
List actualPortletList = layoutTypePortlet.getPortletIds();
```
