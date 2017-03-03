---
title: xposed安装
date: 2017-02-27 17:02:22
tags:
---

[Nexus6 Android 6.0.1 (Marshmallow)刷root教程](http://www.jianshu.com/p/92edb544dc22)

[Xposed框架的安装](http://www.snowdream.tech/2016/09/02/android-install-xposed-framework/)


### twrp

```bash
adb reboot bootloader
fastboot flash recovery twrp.img

fastboot reboot
```

### super su

```bash
 adb push SuperSU-v2.79-201612051815.zip /sdcard/
 adb reboot recovery
 选择从sd卡安装
 reboot system
```

### xposed framework

```bash
adb push xposed-v87-sdk23-arm.zip /sdcard/
adb reboot recovery
 选择从sd卡安装
 reboot system
```

### xposed installer
```bash
adb install XposedInstaller_3.1.1.apk
```







