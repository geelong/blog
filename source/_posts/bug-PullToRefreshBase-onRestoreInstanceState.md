---
title: bug_PullToRefreshBase.onRestoreInstanceState
date: 2016-12-14 19:50:09
tags:
---

错误堆栈如下

```java

Caused by: java.lang.IllegalArgumentException: Wrong state class, expecting View State but received class android.support.v7.widget.RecyclerView$SavedState instead. This usually happens when two views of different type have the same id in the same hierarchy. This view's id is id/test_recycler_view. Make sure other views do not use the same id.
	at android.view.View.onRestoreInstanceState(View.java:13978)
	at com.test.pullrefreshview.PullToRefreshBase.onRestoreInstanceState(PullToRefreshBase.java:880)
	at android.view.View.dispatchRestoreInstanceState(View.java:13954)
	at android.view.ViewGroup.dispatchRestoreInstanceState(ViewGroup.java:3025)
	at android.view.ViewGroup.dispatchRestoreInstanceState(ViewGroup.java:3031)
	at android.view.View.restoreHierarchyState(View.java:13932)
	at android.support.v4.app.Fragment.restoreViewState(Fragment.java:465)
	at android.support.v4.app.FragmentManagerImpl.moveToState(FragmentManager.java:1094)
	at android.support.v4.app.FragmentManagerImpl.moveToState(FragmentManager.java:1252)
	at android.support.v4.app.FragmentManagerImpl.moveToState(FragmentManager.java:1234)
	at android.support.v4.app.FragmentManagerImpl.dispatchActivityCreated(FragmentManager.java:2046)
	at android.support.v4.app.FragmentController.dispatchActivityCreated(FragmentController.java:174)
	at android.support.v4.app.FragmentActivity.onStart(FragmentActivity.java:597)
	
```

<!--more-->

报错的代码

api 19 View.java

```java
    protected void onRestoreInstanceState(Parcelable state) {
        mPrivateFlags |= PFLAG_SAVE_STATE_CALLED;
        if (state != BaseSavedState.EMPTY_STATE && state != null) {
            throw new IllegalArgumentException("Wrong state class, expecting View State but "
                    + "received " + state.getClass().toString() + " instead. This usually happens "
                    + "when two views of different type have the same id in the same hierarchy. "
                    + "This view's id is " + ViewDebug.resolveId(mContext, getId()) + ". Make sure "
                    + "other views do not use the same id.");
        }
    }
```


PullToRefreshBase.java

```java
  @Override
    protected final void onRestoreInstanceState(Parcelable state) {
        if (state instanceof Bundle) {
            Bundle bundle = (Bundle) state;

            setMode(Mode.mapIntToValue(bundle.getInt(STATE_MODE, 0)));
            mCurrentMode = Mode.mapIntToValue(bundle.getInt(STATE_CURRENT_MODE, 0));

            mScrollingWhileRefreshingEnabled = bundle.getBoolean(STATE_SCROLLING_REFRESHING_ENABLED, false);
            mShowViewWhileRefreshing = bundle.getBoolean(STATE_SHOW_REFRESHING_VIEW, true);

            // Let super Restore Itself
            super.onRestoreInstanceState(bundle.getParcelable(STATE_SUPER));

            // State viewState = State.mapIntToValue(bundle.getInt(STATE_STATE, 0));
            // if (viewState == State.REFRESHING || viewState == State.MANUAL_REFRESHING) {
            // setIntegrity(viewState, true);
            // }

            // Now let derivative classes restore their state
            onPtrRestoreInstanceState(bundle);
            return;
        }

        super.onRestoreInstanceState(state);
    }
    
     @Override
    protected final Parcelable onSaveInstanceState() {
        Bundle bundle = new Bundle();

        // Let derivative classes get a chance to save state first, that way we
        // can make sure they don't overrite any of our values
        onPtrSaveInstanceState(bundle);

        bundle.putInt(STATE_STATE, mState.getIntValue());
        bundle.putInt(STATE_MODE, mMode.getIntValue());
        bundle.putInt(STATE_CURRENT_MODE, mCurrentMode.getIntValue());
        bundle.putBoolean(STATE_SCROLLING_REFRESHING_ENABLED, mScrollingWhileRefreshingEnabled);
        bundle.putBoolean(STATE_SHOW_REFRESHING_VIEW, mShowViewWhileRefreshing);
        bundle.putParcelable(STATE_SUPER, super.onSaveInstanceState());

        return bundle;
    }
```

### 过程

- 最开始通过id搜索发现并没有重复使用该id
- 后来想到通过uiautomator查看，发现确实存在了重复id
- 重复原因如下，新建的类传入了当前类的attrs，所以id和当前类相同
- 解决办法就是设置一个新的id就好了

```
 @Override
    protected RecyclerView createRefreshableView(Context context, AttributeSet attrs) {
        RecyclerView recyclerView = null;
        recyclerView = new RecyclerView(context, attrs);

//        需要设置一个新的id,否则id会和当前类相同，导致页面被回收再恢复后抛出异常
//        recyclerView.setId(android.R.id.list);
        return recyclerView;
    }
```

#### 原因

- Activity回收前，通过onSaveInstance告知View存储自己的状态
- ViewGroup先存储自己的状态，以id为key存起来
- ViewGroup的子View储自己的状态，以id为key存起来
- 所以id相同的时候，子View的状态把之前的状态覆盖掉了
- 恢复的时候，父View用id取状态，取出了子View的状态，类型不对。所以抛出异常


ViewGrop.java

```java
 @Override
    protected void dispatchSaveInstanceState(SparseArray<Parcelable> container) {
        super.dispatchSaveInstanceState(container);
        final int count = mChildrenCount;
        final View[] children = mChildren;
        for (int i = 0; i < count; i++) {
            View c = children[i];
            if ((c.mViewFlags & PARENT_SAVE_DISABLED_MASK) != PARENT_SAVE_DISABLED) {
                c.dispatchSaveInstanceState(container);
            }
        }
    }
    
        @Override
    protected void dispatchRestoreInstanceState(SparseArray<Parcelable> container) {
        super.dispatchRestoreInstanceState(container);
        final int count = mChildrenCount;
        final View[] children = mChildren;
        for (int i = 0; i < count; i++) {
            View c = children[i];
            if ((c.mViewFlags & PARENT_SAVE_DISABLED_MASK) != PARENT_SAVE_DISABLED) {
                c.dispatchRestoreInstanceState(container);
            }
        }
    }
```

View.java

```java
    protected void dispatchSaveInstanceState(SparseArray<Parcelable> container) {
        if (mID != NO_ID && (mViewFlags & SAVE_DISABLED_MASK) == 0) {
            mPrivateFlags &= ~PFLAG_SAVE_STATE_CALLED;
            Parcelable state = onSaveInstanceState();
            if ((mPrivateFlags & PFLAG_SAVE_STATE_CALLED) == 0) {
                throw new IllegalStateException(
                        "Derived class did not call super.onSaveInstanceState()");
            }
            if (state != null) {
                // Log.i("View", "Freezing #" + Integer.toHexString(mID)
                // + ": " + state);
                container.put(mID, state);
            }
        }
    }
    
     protected void dispatchRestoreInstanceState(SparseArray<Parcelable> container) {
        if (mID != NO_ID) {
            Parcelable state = container.get(mID);
            if (state != null) {
                // Log.i("View", "Restoreing #" + Integer.toHexString(mID)
                // + ": " + state);
                mPrivateFlags &= ~PFLAG_SAVE_STATE_CALLED;
                onRestoreInstanceState(state);
                if ((mPrivateFlags & PFLAG_SAVE_STATE_CALLED) == 0) {
                    throw new IllegalStateException(
                            "Derived class did not call super.onRestoreInstanceState()");
                }
            }
        }
    }
```



