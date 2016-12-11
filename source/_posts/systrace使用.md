---
title: systrace使用
date: 2016-12-01 16:39:56
tags:
---


参考文章：

- [性能工具Systrace](http://gityuan.com/2016/01/17/systrace/)
- [Android性能优化之Systrace工具介绍(一)](http://androidperformance.com/2015/01/30/android-performance-tools-systrace-1.html)


使用要求：

- eng或userdebug版才能用


使用命令

```bash
pwd
/Users/geelong/developer/android-sdk-macosx/platform-tools/systrace

 python systrace.py --time=3 -o mynewtrace.html sched gfx view wm
 
 python systrace.py --time=3 gfx input view sched freq wm am workq res dalvik sync  disk load hal rs  -o idle -o 1.html

```


systrace和traceview的区别

- systrace除了能看到自己进程的信息，还能看到别的jin进程的信息
- systrace能看到cpu正在执行的任务，可以分析是否cpu被抢占导致执行缓慢。traceview只能看到执行方法时间的长短，不能看到等待cpu这个过程
- systrace能看到GC
- systrace能看到哪一帧卡顿了
- traceview能看到自己app内的方法的执行时间
- systrace是宏观的，过程相关，能看出哪个过程（layout、measure、draw）耗时长,但是是业务无关的，看不出具体是那块业务的layout时间长。traceview是微观的，能看出哪个具体方法耗时长
- 通过Systrace的数据，可以大体上的发现是否存在性能问题。但如果要知道具体情况，就需要用到另外一个工具。 
 
6.TraceView





### 卡顿的原因

- measure、layout、draw慢
	- Hierarchy Viewer
- gc
- cpu抢占
	- 降低不重要线程的优先级 android.os.Process#THREAD_PRIORITY_BACKGROUND 

### 优化重点
- 执行次数少，但时间很长
- 时间短，但执行次数很多
- 火焰图才比较容易看出来

#### 从这里看出，执行时间比较短，但是等待时间就比较长

```
Title	performTraversals
Category	android
User Friendly Category	other
Start	2,795.563 ms
Wall Duration	 229.065 ms
CPU Duration	97.303 ms
Self Time	 1.374 ms
CPU Self Time	 1.008 ms
Description	
A drawing traversal of the View hierarchy, comprised of all layout and drawing needed to produce the frame.

```

## TraceView使用
- 几个方法显示cpu时间200多ms,优化完后，进行对比，效果并不明显
	- 通过log的方式，发现多的只需要30-50ms
	- 再抓一次traceview，部分方法的cpu时间变为1ms
	- [对性能影响比较大：Android CPU FlameGraph](http://blog.csdn.net/oujunli/article/details/50575115)
	- [TraceView 自动化抓取方案实践](https://testerhome.com/topics/3505)

## dmtracedump使用
- [官网](https://developer.android.com/studio/profile/traceview.html#dmtracedump)
- [demo](http://tangzm.com/blog/?p=16)

---

遇到的问题

Android systrace "Could not find an importer for the provided eventData" error resolution

1. open chrome browser and go to "chrome://tracing"

2. in the tracing page, click load and select the systrace generated html file

This works for me.