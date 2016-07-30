---
title: 在Mac 10.11编译Android 6.0
date: 2016-07-30 22:59:22
tags:
---

# 硬件
mac 10.11

nexus6

# 软件
jdk1.7

其它软件信息参考官网，很久之前就编译过5.0，后来mac升级到10.11就编译不了了，各种报错。



[参考文章1:官网源码下载和环境准备地址](https://source.android.com/source/initializing.html)

[参考文章2:在Mac 10.11编译最新的Android 6.0](
http://blog.zhaiyifan.cn/2015/11/24/BuildAndroid6OnMacElCapitan/)



# 前言
1. 编译一个userdebug环境的好处是可以debug各种源码，包括system process等，比光看源码要容易一些。

1. 开发的时候使用也比较方便，在终端直接看APP的数据，使用 adb shell ls /data/data/包名/files 等

1. 之前是想用模拟器的，不过我们的项目只有arm的so库，模拟器兼容性不是很好

# 过程
1. 下载大概是24小时（视网络情况定）
2. 编译大概24小时

# 遇到的问题

```bash
Which would you like? [aosp_arm-eng] 19
build/core/combo/mac_version.mk:38: *****************************************************
build/core/combo/mac_version.mk:39: * Can not find SDK 10.6 at /Developer/SDKs/MacOSX10.6.sdk
build/core/combo/mac_version.mk:40: *****************************************************
build/core/combo/mac_version.mk:41: *** Stop.. Stop.
** Don't have a product spec for: 'aosp_shamu'
** Do you have the right repo manifest?
```


在最后加上10.11 

```bash
12 mac_sdk_versions_supported :=  10.6 10.7 10.8 10.9 10.11
``` 


---


###  java版本不对
```bash
Your version is: java version "1.8.0_65" Java(TM) SE Runtime Environment (build 1.8.0_65-b17) Java HotSpot(TM) 64-Bit Server VM (build 25.65-b01, mixed mode).
The required version is: "1.7.x"
Please follow the machine setup instructions at
https://source.android.com/source/initializing.html
************************************************************
build/core/main.mk:171: *** stop. Stop.
\e[0;31m#### make failed to build some targets (21 seconds) ####\e[00m
bogon:6.0.0_r2 geelong$ java -version
java version "1.8.0_65"
Java(TM) SE Runtime Environment (build 1.8.0_65-b17)
Java HotSpot(TM) 64-Bit Server VM (build 25.65-b01, mixed mode)
```

临时设置Java版本

```bash
export JAVA_HOME=`/usr/libexec/java_home -v 1.7`
export PATH=${JAVA_HOME}/bin:$PATH
```


