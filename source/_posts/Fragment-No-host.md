---
title: Fragment_No_host
date: 2017-03-03 21:10:43
tags:
---

### 问题
- 2个frag,加上左右切换的动画
- 快速切换就会有下面的bug

### 代码

```java

 FragmentManager supportFragmentManager = getSupportFragmentManager();
    supportFragmentManager.beginTransaction()
            .setCustomAnimations(enter, exit)
            .replace(R.id.content, list.get(idx))
            .commitAllowingStateLoss();
    supportFragmentManager.executePendingTransactions();

```

### 堆栈

```bash
java.lang.IllegalStateException: No host
	at android.support.v4.app.FragmentManagerImpl.moveToState(FragmentManager.java:1239)
	at android.support.v4.app.FragmentManagerImpl.moveToState(FragmentManager.java:1234)
	at android.support.v4.app.FragmentManagerImpl.dispatchActivityCreated(FragmentManager.java:2046)
	at android.support.v4.app.Fragment.performActivityCreated(Fragment.java:1989)
	at de.robv.android.xposed.XposedBridge.invokeOriginalMethodNative(Native Method)
	at de.robv.android.xposed.XposedBridge.handleHookedMethod(XposedBridge.java:334)
	at android.support.v4.app.Fragment.performActivityCreated(<Xposed>)
	at android.support.v4.app.FragmentManagerImpl.moveToState(FragmentManager.java:1092)
	at android.support.v4.app.FragmentManagerImpl.moveToState(FragmentManager.java:1252)
	at android.support.v4.app.BackStackRecord.run(BackStackRecord.java:742)
	at android.support.v4.app.FragmentManagerImpl.execPendingActions(FragmentManager.java:1617)
	at android.support.v4.app.FragmentManagerImpl.executePendingTransactions(FragmentManager.java:570)
	at com.abc.IntroductionHighApiActivity$SimpleOnGestureListener.updateFrag(IntroductionHighApiActivity.java:116)
	at com.abc.IntroductionHighApiActivity$SimpleOnGestureListener.onLeftToRight(IntroductionHighApiActivity.java:79)
	at com.abc.IntroductionHighApiActivity$SimpleOnGestureListener.onFling(IntroductionHighApiActivity.java:69)
	at android.view.GestureDetector.onTouchEvent(GestureDetector.java:650)
	at com.abc.IntroductionHighApiActivity.dispatchTouchEvent(IntroductionHighApiActivity.java:124)
	at com.android.internal.policy.PhoneWindow$DecorView.dispatchTouchEvent(PhoneWindow.java:2364)
	at android.view.View.dispatchPointerEvent(View.java:9514)
	at android.view.ViewRootImpl$ViewPostImeInputStage.processPointerEvent(ViewRootImpl.java:4230)
	at android.view.ViewRootImpl$ViewPostImeInputStage.onProcess(ViewRootImpl.java:4096)
	at android.view.ViewRootImpl$InputStage.deliver(ViewRootImpl.java:3642)
	at android.view.ViewRootImpl$InputStage.onDeliverToNext(ViewRootImpl.java:3695)
	at android.view.ViewRootImpl$InputStage.forward(ViewRootImpl.java:3661)
	at android.view.ViewRootImpl$AsyncInputStage.forward(ViewRootImpl.java:3787)
	at android.view.ViewRootImpl$InputStage.apply(ViewRootImpl.java:3669)
	at android.view.ViewRootImpl$AsyncInputStage.apply(ViewRootImpl.java:3844)
	at android.view.ViewRootImpl$InputStage.deliver(ViewRootImpl.java:3642)
	at android.view.ViewRootImpl$InputStage.onDeliverToNext(ViewRootImpl.java:3695)
	at android.view.ViewRootImpl$InputStage.forward(ViewRootImpl.java:3661)
	at android.view.ViewRootImpl$InputStage.apply(ViewRootImpl.java:3669)
	at android.view.ViewRootImpl$InputStage.deliver(ViewRootImpl.java:3642)
	at android.view.ViewRootImpl.deliverInputEvent(ViewRootImpl.java:5922)
	at android.view.ViewRootImpl.doProcessInputEvents(ViewRootImpl.java:5896)
	at android.view.ViewRootImpl.enqueueInputEvent(ViewRootImpl.java:5857)
	at android.view.ViewRootImpl$WindowInputEventReceiver.onInputEvent(ViewRootImpl.java:6025)
	at android.view.InputEventReceiver.dispatchInputEvent(InputEventReceiver.java:185)
	at android.os.MessageQueue.nativePollOnce(Native Method)
	at android.os.MessageQueue.next(MessageQueue.java:323)
	at android.os.Looper.loop(Looper.java:135)
	at android.app.ActivityThread.main(ActivityThread.java:5417)
	at java.lang.reflect.Method.invoke(Native Method)
	at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:726)
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:616)
	at de.robv.android.xposed.XposedBridge.main(XposedBridge.java:102)
	
```

### 原因

有移除动画的时候，移除动画结束后，才会调用frag的destory方法，在destory没有执行前，再次把frag加进去，就会出错

FragmentManagerImpl
- 版本23.4.0
- 1140行

```java

if (f.mView != null && f.mContainer != null) {
    Animation anim = null;
    if (mCurState > Fragment.INITIALIZING && !mDestroyed) {
        anim = loadAnimation(f, transit, false,
                transitionStyle);
    }
    if (anim != null) {
        final Fragment fragment = f;
        f.mAnimatingAway = f.mView;
        f.mStateAfterAnimating = newState;
        final View viewToAnimate = f.mView;
        anim.setAnimationListener(new AnimateOnHWLayerIfNeededListener(
                viewToAnimate, anim) {
            @Override
            public void onAnimationEnd(Animation animation) {
                super.onAnimationEnd(animation);
                if (fragment.mAnimatingAway != null) {
                    fragment.mAnimatingAway = null;
                    moveToState(fragment, fragment.mStateAfterAnimating,
                            0, 0, false);
                }
            }
        });
        f.mView.startAnimation(anim);
    }
    f.mContainer.removeView(f.mView);
}

```

### 解决方案

动画开始前，fragment会设置一个mRemoving = true，结束后会把状态还原，所以在removing的时候，不要操作fragment就好了


FragmentManagerImpl

```java
动画开始前

    public void removeFragment(Fragment fragment, int transition, int transitionStyle) {
        if (DEBUG) Log.v(TAG, "remove: " + fragment + " nesting=" + fragment.mBackStackNesting);
        final boolean inactive = !fragment.isInBackStack();
        if (!fragment.mDetached || inactive) {
            if (mAdded != null) {
                mAdded.remove(fragment);
            }
            if (fragment.mHasMenu && fragment.mMenuVisible) {
                mNeedMenuInvalidate = true;
            }
            fragment.mAdded = false;
            fragment.mRemoving = true;
            moveToState(fragment, inactive ? Fragment.INITIALIZING : Fragment.CREATED,
                    transition, transitionStyle, false);
        }
    }
 
动画结束后

    void makeInactive(Fragment f) {
        if (f.mIndex < 0) {
            return;
        }
        
        if (DEBUG) Log.v(TAG, "Freeing fragment index " + f);
        mActive.set(f.mIndex, null);
        if (mAvailIndices == null) {
            mAvailIndices = new ArrayList<Integer>();
        }
        mAvailIndices.add(f.mIndex);
        mHost.inactivateFragment(f.mWho);
        f.initState();
    }
    
```