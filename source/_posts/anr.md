---
title: anr
date: 2017-01-07 11:46:01
tags:
---

### [android开篇](http://gityuan.com/android/)

- init
	- 是所有用户进程的鼻祖
- zygote
	- 是所有Java进程的父进程
	- 加载ZygoteInit类，注册Zygote Socket服务端套接字
	- 加载虚拟机
	- preloadClasses
	- preloadResouces
- system_server
	- Zygote孵化的第一个进程
	- 负责启动和管理整个Java framework，包含ActivityManager，PowerManager等服务
- Media Server进程
	- 由init进程fork而来
	- 负责启动和管理整个C++ framework，包含AudioFlinger，Camera Service，等服务

---	

### ANR

- [理解Android ANR的触发情景](http://gityuan.com/2016/07/02/android-anr/)
- [理解Android ANR的处理过程](http://gityuan.com/2016/12/02/app-not-response/)


---
### input系统
- [Input系统—启动篇](http://gityuan.com/2016/12/10/input-manager/)
	- ./frameworks/native/include/input/InputTransport.h
- [Input系统—InputReader线程](http://gityuan.com/2016/12/11/input-reader/)
- [Input系统—InputDispatcher线程](http://gityuan.com/2016/12/17/input-dispatcher/)
- [Input系统—UI线程](http://gityuan.com/2016/12/24/input-ui/)
- [Input系统—进程交互](http://gityuan.com/2016/12/31/input-ipc/)
- [Input系统—ANR原理分析](http://gityuan.com/2017/01/01/input-anr/)


