---
title: event_log
date: 2017-01-07 11:13:19
tags:
---

[Android EventLog含义](http://gityuan.com/2016/05/15/event-log/)

例子：

```bash

52004 binder_sample (descriptor|3),(method_num|1|5),(time|1|3),(blocking_package|3),(sample_percent|1|6)
12-30 10:12:51.792 22854 22854 I binder_sample: [android.hardware.input.IInputManager,5,5052,com.android.commands.monkey,100]
```


- blocking_package=com.android.commands.monkey
- descriptor=android.hardware.input.IInputManager
- method_num=5
- time=5052ms
- sample_percent=100

>一个疑问 22854是什么？


---

