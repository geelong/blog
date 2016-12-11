layout: dumpsys
title: gfxinfo源码
date: 2016-11-30 22:50:46
tags:
---

通过adb shell dumpsys gfxinfo 包名 收集APP的绘制信息（Draw Process Execute），每秒收集1次。那么问题提来，下一秒收集的信息会不会和上一秒重复了呢？

<!--more-->

## 源码分析

主要参考这篇文章[dumpsys 实现原理](http://gold.xitu.io/entry/57be9f167db2a20068e2d757)，但是这篇文章没有解决我的问题


[ActivityThread](https://android.googlesource.com/platform/frameworks/base/+/android-4.4.2_r1/core/java/android/app/ActivityThread.java)

```java

@Override
public void dumpGfxInfo(FileDescriptor fd, String[] args) {
    dumpGraphicsInfo(fd);
    WindowManagerGlobal.getInstance().dumpGfxInfo(fd);
}
```

[WindowManagerGlobal](https://android.googlesource.com/platform/frameworks/base/+/android-4.4.2_r1/core/java/android/view/WindowManagerGlobal.java)

```java
 public void dumpGfxInfo(FileDescriptor fd) {
        FileOutputStream fout = new FileOutputStream(fd);
        PrintWriter pw = new FastPrintWriter(fout);
        try {
            synchronized (mLock) {
                final int count = mViews.size();
                pw.println("Profile data in ms:");
                for (int i = 0; i < count; i++) {
                    ViewRootImpl root = mRoots.get(i);
                    String name = getWindowName(root);
                    pw.printf("\n\t%s", name);
                    HardwareRenderer renderer =
                            root.getView().mAttachInfo.mHardwareRenderer;
                    if (renderer != null) {
                        renderer.dumpGfxInfo(pw);
                    }
                }
                pw.println("\nView hierarchy:\n");
...
    }
    
```   


[HardwareRenderer](https://android.googlesource.com/platform/frameworks/base/+/android-4.4.2_r1/core/java/android/view/HardwareRenderer.java)

```java
@Override
void dumpGfxInfo(PrintWriter pw) {
    if (mProfileEnabled) {
        pw.printf("\n\tDraw\tProcess\tExecute\n");
        mProfileLock.lock();
        try {
            for (int i = 0; i < mProfileData.length; i += PROFILE_FRAME_DATA_COUNT) {
                if (mProfileData[i] < 0) {
                    break;
                }
                pw.printf("\t%3.2f\t%3.2f\t%3.2f\n", mProfileData[i], mProfileData[i + 1],
                        mProfileData[i + 2]);
                mProfileData[i] = mProfileData[i + 1] = mProfileData[i + 2] = -1;
            }
            mProfileCurrentFrame = mProfileData.length;
        } finally {
            mProfileLock.unlock();
        }
    }
}

```
这是关键代码，从这里可以看出，信息打印完之后，会设置为空，所以不会出现下一秒的输出，包含上一秒的情况


## 找的过程很复杂，有没有更快捷的？

### 在源码处查找



```bash
grep -rn "Draw\\\tProcess\\\tExecute" .

./android/view/HardwareRenderer.java:992:                pw.printf("\n\tDraw\tProcess\tExecute\n");


需要把\t 转义 为 \\\t



这样会更简单

grep -rn "Draw" . | grep Process
./android/view/HardwareRenderer.java:992:                pw.printf("\n\tDraw\tProcess\tExecute\n");

```

