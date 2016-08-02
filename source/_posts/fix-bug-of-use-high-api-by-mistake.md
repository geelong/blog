---
title: 修复一个误用高版本系统api的bug和感悟
date: 2016-08-01 20:22:25
tags:
- lint
- todo
- bugfix

---

# 起因
我们的app从api=10开始支持，然后写了这么一行代码

```java
view.setAlpha(0);
```

一路测试都是顺利的，直到灰度测试后，在2.3的系统发现

<!--more-->

```java
java.lang.NoSuchMethodError:android.widget.TextView.setAlpha
```
其实原因很简单，就是setAlpha方法是在api=11的时候加的，因为用了instantRun(minSdkVersion 21)所以开发的时候ide一直没有警告

# 改进
## 过程
在build.gradle添加lint检查，如下

```bash
 lintOptions {
        abortOnError false
        check 'NewApi', 'InlinedApi'
    }
```

```bash
./gradlew lint
```
然后看生成的报告，就能直到哪些用了超过最低支持版本的api了，对于那些做好了版本判断和明确是高版本才会调用的方法需要加@TargetApi，避免每次报告都生成一大堆信息。 **信息太多了就不是信息了**

```java
	@TargetApi(11)
    public MyView(Context context, AttributeSet attrs, int defStyle) 
```

## 意义
### 找出同类问题
### 避免以后发生类似问题
解决问题只解决看到的问题是最基础的，然后是解决现在的同类问题，由这个问题发现的其它问题，如何避免后续发生类似的问题

# todo
需要执行命令才能看到报告，难免后来某次紧急修改没有做这一步，引入了类似问题。但是每次都生成报告，会影响打包速度，毕竟不是每次都能分析出问题的。如何完善，继续思考。
