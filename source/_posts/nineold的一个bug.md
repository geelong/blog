---
title: nineold的一个bug
date: 2017-01-20 18:44:06
tags:
---

### 问题
发现nineold的一个bug,但是无法定位是那块业务的问题

<!--more-->

堆栈和源码如下

```java

java.lang.IndexOutOfBoundsException: Invalid index 3, size is 3
	at java.util.ArrayList.throwIndexOutOfBoundsException(ArrayList.java:255)
	at java.util.ArrayList.get(ArrayList.java:308)
	at com.nineoldandroids.animation.ValueAnimator$AnimationHandler.handleMessage(ValueAnimator.java:649)
	at android.os.Handler.dispatchMessage(Handler.java:102)
	at android.os.Looper.loop(Looper.java:148)
	at android.app.ActivityThread.main(ActivityThread.java:5443)
	at java.lang.reflect.Method.invoke(Native Method)
	at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:728)
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:618)


ValueAnimator.java
	
 	while (i < numAnims) {
649        ValueAnimator anim = animations.get(i);
        if (anim.animationFrame(currentTime)) {
            endingAnims.add(anim);
        }
        if (animations.size() == numAnims) {
            ++i;
        } else {
            // An animation might be canceled or ended by client code
            // during the animation frame. Check to see if this happened by
            // seeing whether the current index is the same as it was before
            // calling animationFrame(). Another approach would be to copy
            // animations to a temporary list and process that list instead,
            // but that entails garbage and processing overhead that would
            // be nice to avoid.
            --numAnims;
            endingAnims.remove(anim);
        }
    }	
```

### 定位过程
1. 经过分析，animations的变化只会在endAnimation()方法里减少
2. 所以，在进行animations遍历的时候，如果执行了endAnimation，就把堆栈上传到server
3. 代码如下

```java
ValueAnimator.java

public void handleMessage(Message msg) {
	mIsFrame.set(true);
	while (i < numAnims) {
	    ValueAnimator anim = animations.get(i);
	    if (anim.animationFrame(currentTime)) {
	        endingAnims.add(anim);
	    }
	    if (animations.size() == numAnims) {
	        ++i;
	    } else {
	        // An animation might be canceled or ended by client code
	        // during the animation frame. Check to see if this happened by
	        // seeing whether the current index is the same as it was before
	        // calling animationFrame(). Another approach would be to copy
	        // animations to a temporary list and process that list instead,
	        // but that entails garbage and processing overhead that would
	        // be nice to avoid.
	        --numAnims;
	        endingAnims.remove(anim);
	    }
	}
	mIsFrame.set(false);
}	

private void endAnimation() {
    if (mIsFrame.get()) {//执行animationFrame的时候，endAnimation是危险的
        IExceptionUploader uploader = ExceptionUploaderFactory.getExceptionUploader();
        if (uploader != null) {
            uploader.onException(new Exception());
        }
    }
}    

```


### 结论
1. 捕获了导致这个问题的业务堆栈，这种调用情况，是无论怎么看代码都不可能想到的
2. 使用没有明显问题，所以决定修改nineold的源码。在循环的时候，发现移除动画了，就更新一下数组的总量
3. 修改后代码如下

```java

while (i < numAnims) {
    ValueAnimator anim = animations.get(i);
    if (anim.animationFrame(currentTime)) {
        endingAnims.add(anim);
    }
    if (animations.size() == numAnims) {
        ++i;
    } else {
        // An animation might be canceled or ended by client code
        // during the animation frame. Check to see if this happened by
        // seeing whether the current index is the same as it was before
        // calling animationFrame(). Another approach would be to copy
        // animations to a temporary list and process that list instead,
        // but that entails garbage and processing overhead that would
        // be nice to avoid.
        numAnims = animations.size();
        endingAnims.remove(anim);
    }
}
                    
```