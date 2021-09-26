---
title: Failed building wheel for cryptography error can‘t find Rust compiler
date: 2021-06-01 11:28:55
tags:
- python
categories:
---

```bash
1.问题记录
执行pip install -r requirements.txt时报下述error：
error: can't find Rust compiler
This package requires Rust >=1.41.0.
Failed building wheel for cryptography
Failed cleaning build dir for cryptography
2.问题解决
（1）安装rust编译器： https://www.rust-lang.org/tools/install
（2）升级pip后重新执行pip install
    $ easy_install pip
    $ pip install -r requirements.txt
```
