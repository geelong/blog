---
title: RN_started
date: 2016-09-14 12:10:33
tags:
---


### hello world新建和运行
```bash
react-native init AwesomeProject
cd AwesomeProject
react-native run-android
```

### 开发的时候是从下面地址下载了js

http://localhost:8081/index.android.bundle?platform=android&dev=true&hot=false&minify=false

也可以保存这个文件放到APP上，app直接加载这个文件，就不用开服务器了

### (Android 5.0及以上)使用adb reverse命令
注意，这个选项只能在5.0以上版本(API 21+)的安卓设备上使用。
首先把你的设备通过USB数据线连接到电脑上，并开启USB调试（关于如何开启USB调试，参见上面的章节）。

1. 运行adb reverse tcp:8081 tcp:8081
1. 不需要更多配置，你就可以使用Reload JS和其它的开发选项了。

### (Android 5.0以下)通过Wi-Fi连接你的本地开发服务器
1. 首先确保你的电脑和手机设备在同一个Wi-Fi环境下。
1. 在设备上运行你的React Native应用。和打开其它App一样操作。
1. 你应该会看到一个“红屏”错误提示。这是正常的，下面的步骤会解决这个报错。
1. 摇晃设备，或者运行adb shell input keyevent 82，可以打开开发者菜单。
1. 点击进入Dev Settings。
1. 点击Debug server host for device。
1. 输入你电脑的IP地址和端口号（譬如10.0.1.1:8081）。在Mac上，你可以在系统设置/网络里找查询你的IP地址。在Windows上，打开命令提示符并输入ipconfig来查询你的IP地址。在Linux上你可以在终端中输入ifconfig来查询你的IP地址。
1. 回到开发者菜单然后选择Reload JS。


### 生成最终的apk

这里会把js文件打到项目assets里面

```bash
cd android && ./gradlew assembleRelease


unzip -l  app/build/outputs/apk/app-release-unsigned.apk 
assets/index.android.bundle
```

### 参考文章：
- [在设备上运行](http://reactnative.cn/docs/0.31/running-on-device-android.html#content)
- [生成已签名的APK](http://reactnative.cn/docs/0.31/signed-apk-android.html#content)
- [从源代码编译React Native](http://reactnative.cn/docs/0.31/android-building-from-source.html#content)