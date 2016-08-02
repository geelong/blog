---
title: android测试题
date: 2016-08-01 22:47:02
tags:
---
# ActivityA打开ActivityB这个过程生命周期是怎样的？(1)
1. a.onPause->b.onCreate->b.onStart->b.onResume->a.onStop
1. a.onPause->a.onStop->b.onCreate->b.onStart->b.onResume
1. a.onPause->a.onStop->a.onDestory->b.onCreate->b.onStart->b.onResume
1. a.onPause->a.onStop->b.onCreate->b.onResume->b.onStart

# BroadcastReceiver ANR时间是多少(2)
1. 5s
2. 10s
3. 15s
4. 20s


