---
title: am_broadcast
date: 2016-11-14 17:44:14
tags:
---

adb发送广播的使用

```bash
adb shell am broadcast -a actionname --es paramname paramval
```

参数前是 --（2个-），之前用了一个-，一直都不好用


> 直接用adb发广播的好处有很多，最明显就是测试的效率提高，不需要做很多前提的准备，才能走相应逻辑