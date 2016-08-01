---
title: Android6.0 userdebug版安装busybox
tags:
  - busybox
date: 2016-08-01 00:12:21
---


# 下载对应版本的busybox

[下载地址](https://busybox.net/downloads/binaries/)

# 安装
<!-- more -->
## remount system不上

```bash
root@shamu:/system/xbin # cd /system/xbin/                                     
root@shamu:/system/xbin # mount -o remount,rw -t yaffs2 /dev/block/mtdblock3 />
mount: Permission denied
```
## 解决办法

```bash
bogon:~ geelong$ adb root
adbd is already running as root
bogon:~ geelong$ adb remount
dm_verity is enabled on the system partition.
Use "adb disable-verity" to disable verity.
If you do not, remount may succeed, however, you will still not be able to write to these volumes.
remount of /system failed: Read-only file system
remount failed
bogon:~ geelong$ adb disable-verity
Verity disabled on /system
Now reboot your device for settings to take effect
bogon:~ geelong$ adb reboot
bogon:~ geelong$ adb root
restarting adbd as root
bogon:~ geelong$ adb remount
remount succeeded
bogon:~ geelong$ adb shell
root@shamu:/ # cd /sys
sys/    system/ 
root@shamu:/ # cd /system/xbin/                                                
root@shamu:/system/xbin # ll
/system/bin/sh: ll: not found
127|root@shamu:/system/xbin # cp /sdcard/test/busybox    
1|root@shamu:/system/xbin # ./busybox --install .
```
# 添加ll命令

```bash
vi ll
ls -l $@
```