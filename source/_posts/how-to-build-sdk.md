---
title: 编译Android sdk,添加隐藏代码
date: 2016-09-07 22:19:23
tags:

- 编译sdk
- 查看隐藏api

---

### 意义
- 看View等源码不飘红了，看着更方便
- 单步跟踪更方便
- 不用经常打开庞大的aosp项目，看个别方法的实现。提高开发效率
- 其它app运行在aosp的机器上时，可以通过debug查看调用了那些系统api

### 环境
- Android aosp 6.0(23)
- java 1.7
- mac

### 编译sdk步骤
#### 初始化编译环境

```
source build/envsetup.sh
```

#### 设定编译目标

```
lunch sdk-eng
```

#### 通过以下命令编译SDK

```
make sdk

成功输出
\e[0;32m#### make completed successfully (01:34:53 (hh:mm:ss)) ####\e[00m
```

<!--more-->


### 编译完成后产物
#### classes-full-debug.jar 
- 路径： out\target\common\obj\JAVA_LIBRARIES\framework_intermediates
- 说明： 只有字节码，没有资源

#### sdk（android.jar）
- 路径： out\target\common\sdk\sdk
- 说明： 有字节码和资源


### 添加隐藏代码的步骤
#### 合并编译好的代码
class-full-debug.jar和android.jar合并，前者解压的文件拷贝到后者解压的文件夹中去，然后重新压缩成android.jar。把生成的sdk拷贝到已有sdk目录对应路径下
#### 在已有sdk源码目录添加下面源码
- frameworks/base/core/java
- /out/target/common/obj/JAVA_LIBRARIES/framework_intermediates/src/core/java

#### 后续需要看的类也可以根据这种方式，把class添加到android.jar,把Java代码放到源码目录下


### 结果
- View不飘红了
- 可以直接查看ActivityThread
- 还有部分第三方类不可以查看，可以根据上面方法继续添加


### 遇到的问题

#### java环境不对

```bash
Copying: out/target/common/obj/JAVA_LIBRARIES/android-support-v7-palette_intermediates/classes.jar
Docs droiddoc: out/target/common/docs/offline-sdk
frameworks/base/media/java/android/media/SRTRenderer.java:168: 警告: '_' 用作标识符
        final int _ = 0;
                  ^
  (Java SE 8 之后的发行版中可能不支持使用 '_' 作为标识符)
frameworks/base/media/java/android/media/SRTRenderer.java:187: 警告: '_' 用作标识符
            Message msg = mEventHandler.obtainMessage(MEDIA_TIMED_TEXT, _, _, parcel);
                                                                        ^
  (Java SE 8 之后的发行版中可能不支持使用 '_' 作为标识符)
frameworks/base/media/java/android/media/SRTRenderer.java:187: 警告: '_' 用作标识符
            Message msg = mEventHandler.obtainMessage(MEDIA_TIMED_TEXT, _, _, parcel);
                                                                           ^
  (Java SE 8 之后的发行版中可能不支持使用 '_' 作为标识符)
javadoc: 警告 - 找到程序包 "android.media.effect" 的多个程序包注释源
/Volumes/android/6.0.0_r2/frameworks/base/docs/html/guide/topics/ui/drag-drop.jd:-980: error 101: Unresolved link/see tag "android.view.View.DragShadowBuilder#View.DragShadowBuilder()" in [null]
/Volumes/android/6.0.0_r2/frameworks/base/docs/html/guide/topics/ui/drag-drop.jd:-980: error 101: Unresolved link/see tag "android.view.View.DragShadowBuilder#View.DragShadowBuilder(View)" in [null]
frameworks/base/core/java/android/app/job/JobScheduler.java:22: error 101: Unresolved link/see tag "android.app.job.JobInfo.Builder#JobInfo.Builder(int,android.content.ComponentName)" in android.app.job.JobScheduler
frameworks/base/core/java/android/view/View.java:19484: error 101: Unresolved link/see tag "#View.DragShadowBuilder()" in android.view.View.DragShadowBuilder
frameworks/base/core/java/android/view/View.java:19484: error 101: Unresolved link/see tag "#View.DragShadowBuilder(View)" in android.view.View.DragShadowBuilder
DroidDoc took 185 sec. to write docs to out/target/common/docs/offline-sdk
4 个警告
make: *** [out/target/common/docs/offline-sdk-timestamp] Error 45

\e[0;31m#### make failed to build some targets (01:20:45 (hh:mm:ss)) ####\e[00m
```
##### 设置jdk 版本为1.7
~~~bash
export JAVA_HOME=`/usr/libexec/java_home -v 1.7`
export PATH=${JAVA_HOME}/bin:$PATH
~~~

### 压缩android.jar的路径不对

```
致命错误: 在类路径或引导类路径中找不到程序包 java.lang
```

原因：
对android.jar解压缩后合并的文件的父文件夹进行压缩，导致多了一层文件夹。用zip命令，对所有合并完成的文件进行压缩就好了

