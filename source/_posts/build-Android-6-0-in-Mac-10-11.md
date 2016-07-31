---
title: 在Mac 10.11编译Android 6.0
date: 2016-07-30 22:59:22
tags:
- android
- aosp
- Android6.0
- mac


---

# 前言
1. 编译一个userdebug环境的好处是可以debug各种源码，包括system process等，比光看源码要容易一些。

1. 开发的时候使用也比较方便，在终端直接看APP的数据，使用 adb shell ls /data/data/包名/files 等

1. 之前是想用模拟器的，不过我们的项目只有arm的so库，模拟器兼容性不是很好

<!-- more -->


# 硬件
mac 10.11

nexus6

# 软件
jdk1.7

其它软件信息参考官网，很久之前就编译过5.0，后来mac升级到10.11就编译不了了，各种报错。



[参考文章1:官网源码下载和环境准备地址](https://source.android.com/source/initializing.html)

[参考文章2:在Mac 10.11编译最新的Android 6.0](
http://blog.zhaiyifan.cn/2015/11/24/BuildAndroid6OnMacElCapitan/)


# 过程

## 下载
大概需要24小时（视网络情况定）
## 编译
大概需要3个小时  
编译成功的信息

```bash
Install system fs image: out/target/product/shamu/system.img
out/target/product/shamu/system.img+out/target/product/shamu/obj/PACKAGING/recovery_patch_intermediates/recovery_from_boot.p maxsize=2192424960 blocksize=135168 total=669161813 reserve=22167552
\e[0;32m#### make completed successfully (03:06:45 (hh:mm:ss)) ####\e[00m
```

## 安装
### 进入bootloader模式

`$ adb reboot bootloader`

### 刷入系统

`$ fastboot flashall -w`

刷机信息  

```bash
bogon:6.0.0_r2 geelong$ adb reboot bootloader
* daemon not running. starting it now on port 5037 *
* daemon started successfully *
bogon:6.0.0_r2 geelong$ fastboot flashall -w
target reported max download size of 536870912 bytes
Creating filesystem with parameters:
    Size: 59751923712
    Block size: 4096
    Blocks per group: 32768
    Inodes per group: 8192
    Inode size: 256
    Journal blocks: 32768
    Label: 
    Blocks: 14587872
    Block groups: 446
    Reserved block group size: 1024
Created filesystem with 11/3653632 inodes and 275392/14587872 blocks
Creating filesystem with parameters:
    Size: 268435456
    Block size: 4096
    Blocks per group: 32768
    Inodes per group: 8192
    Inode size: 256
    Journal blocks: 1024
    Label: 
    Blocks: 65536
    Block groups: 2
    Reserved block group size: 15
Created filesystem with 11/16384 inodes and 2089/65536 blocks
--------------------------------------------
Bootloader Version...: moto-apq8084-71.15
Baseband Version.....: D4.01-9625-05.32+FSG-9625-02.109
Serial Number........: ZX1G523RG4
--------------------------------------------
checking product...
OKAY [  0.002s]
sending 'boot' (8203 KB)...
OKAY [  0.262s]
writing 'boot'...
OKAY [  0.243s]
sending 'recovery' (8803 KB)...
OKAY [  0.279s]
writing 'recovery'...
OKAY [  0.120s]
sending sparse 'system' (524186 KB)...
OKAY [ 16.595s]
writing 'system'...
OKAY [  9.194s]
sending sparse 'system' (128906 KB)...
OKAY [  4.119s]
writing 'system'...
OKAY [  2.375s]
erasing 'userdata'...
OKAY [ 14.425s]
sending 'userdata' (141067 KB)...
OKAY [  4.421s]
writing 'userdata'...
OKAY [  2.079s]
erasing 'cache'...
OKAY [  0.042s]
sending 'cache' (6248 KB)...
OKAY [  0.198s]
writing 'cache'...
OKAY [  0.098s]
rebooting...
finished. total time: 54.463s```

大功告成，不过界面和factory image的不同，里面的APP也不同  


{% asset_img android6.0.png  %}


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


