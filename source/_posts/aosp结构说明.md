---
title: aosp结构说明
date: 2016-12-21 11:28:54
tags:
---

基于6.0.2项目

- ./art/runtime/
	- 运行时，Runtime.java的jni实现
	- ./art/runtime/native/java_lang_Runtime.cc
	- [android linker机制：so的加载和相关库的链接](http://wps2015.org/drops/drops/Android%20Linker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.html)
	- vm实现