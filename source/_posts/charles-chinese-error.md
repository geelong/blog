---
title: 解决Charles中文乱码
date: 2016-09-08 15:38:00
tags:

- Charles
- 中文乱码

---

[Charles设置MapLocal乱码的解决](http://www.paincker.com/charles-charset-incorrect)

主要通过Tools-rewrite给返回的response的header添加Content-Type=application/json;charset=UTF-8
