---
title: Dalvik虚拟机简要介绍和学习计划读书笔记
date: 2016-10-29 23:03:38
tags:

- dalvik

---

[Dalvik虚拟机简要介绍和学习计划](http://blog.csdn.net/luoshengyang/article/details/8852432)

- 介绍了dalvik的基本原理
- dalvik和java虚拟机的关系
	- dalvik基于寄存器，java虚拟机基于堆栈
	- dalviks使用dex文件，相对于Java使用的class文件，压缩更好

<!--more-->

---

- 内存管理
	- HoneyComb之前
		- Bitmap 内存是在native Heap中分配的
		- Bitmap+java object<=java object Heap
		- Bitmap不受gc管理
	- >= HoneyComb(3.0)
		- Bitmap直接在Java Object Heap中分配，接受GC管理

---		

- 垃圾回收
	- <GingerBread(2.3)
		- stop the world, 垃圾回收时，其它线程都停止
		- 一次性手机全部垃圾
		- 程序终止时间大于100ms
	- 	>=GingerBread
		-  cocurrent,垃圾线程和其它线程并非执行
		-  一次只手机一部分垃圾
		-  停止时间少于5ms

---

-  JIT和AOT
	- jit是运行时编译，
		- 运行时，根据运行的信息，把部分代码编译成本机代码(根据架构的不同，使用不同的指令)，加快执行速度
	- aot 是运行前编译，
		- 不影响程序执行速度 
		- 但是不能利用运行的数据进行优化

---

- jni
	-  java的接口大多都是通过jni调用c来实现的
	-  c调用的系统的函数来实现

---
		
- init zygote 和应用进程的关系
	- init进程fork出zygote进程
	- zygote进程加载Java核心的框架
	- zygote进程在需要的时候fork出应用进程，这样应用进程就能共享核心Java库

