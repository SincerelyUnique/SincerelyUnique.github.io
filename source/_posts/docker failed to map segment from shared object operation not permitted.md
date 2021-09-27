---
title: docker failed to map segment from shared object operation not permitted
date: 2020-01-13 15:57:40
tags: 
- docker
categories:
---

好像是昨天在使用docker进行build时，访问/tmp目录，一直报没有权限，就是operation not permitted。

参考StackOverflow remount一下就好了，记录一下

> sudo mount /tmp -o remount,exec

参考地址：https://stackoverflow.com/questions/13502156/what-are-possible-causes-of-failed-to-map-segment-from-shared-object-operation
