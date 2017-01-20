---
title: nineold_循环动画的bug
date: 2017-01-04 23:45:23
tags:
---

### 需求
- 做一系列属性动画，然后把它们循环的播放。于是在onAnimationEnd的时候，执行AnimatorSet的start方法。
- 提供一个stop的接口，供暂停动画，恢复到第一帧的状态

### 问题
点击stop的时候，有一定的概率第一帧的元素位置都不对。代码如下

<!--more-->


```java
public class LoadingLayout extends FrameLayout {

    private AnimatorSet animatorAll;

    public LoadingLayout(Context context) {
        super(context);
        initView();
    }

    public LoadingLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        initView();
    }

    public LoadingLayout(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        initView();
    }

    private LinearLayout feedLoadingFrame1;
    private View feedLoadingFrame1InRect;
    private View feedLoadingFrame1InLine1;
    private View feedLoadingFrame1InLine2;
    private View feedLoadingFrame1InLine3;
    private View feedLoadingFrame1Line1;
    private View feedLoadingFrame1Line2;

    private void initView() {
        LayoutInflater.from(getContext()).inflate(R.layout.layout_feed_loading, this);


        feedLoadingFrame1 = (LinearLayout) findViewById(R.id.feed_loading_frame1);
        feedLoadingFrame1InRect = (View) findViewById(R.id.feed_loading_frame1_in_rect);
        feedLoadingFrame1InLine1 = (View) findViewById(R.id.feed_loading_frame1_in_line1);
        feedLoadingFrame1InLine2 = (View) findViewById(R.id.feed_loading_frame1_in_line2);
        feedLoadingFrame1InLine3 = (View) findViewById(R.id.feed_loading_frame1_in_line3);
        feedLoadingFrame1Line1 = (View) findViewById(R.id.feed_loading_frame1_line1);
        feedLoadingFrame1Line2 = (View) findViewById(R.id.feed_loading_frame1_line2);

//        post(new Runnable() {
//            @Override
//            public void run() {
//                postDelayed(new Runnable() {
//                    @Override
//                    public void run() {
//                     start();
//                    }
//                }, 2000);
//            }
//        });
    }

    private AnimatorSet frame(View... views) {
        AnimatorSet inAnimatorSet = createAnimatorSet(true, views);
        AnimatorSet outAnimatorSet = createAnimatorSet(false, views);
        outAnimatorSet.setStartDelay(FRAME_INTERNAL);

        final AnimatorSet animatorFrame = new AnimatorSet();
        animatorFrame.playSequentially(inAnimatorSet, outAnimatorSet);
        return animatorFrame;
    }

    private AnimatorSet firstFrame() {
        AnimatorSet outAnimatorSet = createAnimatorSet(false,
                feedLoadingFrame1Line2, feedLoadingFrame1Line1,
                feedLoadingFrame1InRect,
                feedLoadingFrame1InLine3, feedLoadingFrame1InLine2, feedLoadingFrame1InLine1);
        return outAnimatorSet;
//        return frame(
//                findViewById(R.id.feed_loading_frame1_1),
//                findViewById(R.id.feed_loading_frame1_2));
    }

//    private AnimatorSet secondFrame(){
//        return frame(
//                findViewById(R.id.feed_loading_frame2_1),
//                findViewById(R.id.feed_loading_frame2_2),
//                findViewById(R.id.feed_loading_frame2_3),
//                findViewById(R.id.feed_loading_frame2_4));
//    }

    public boolean isRunning() {
        return animatorAll != null && animatorAll.isRunning();
    }

    public void stop() {
        if (isRunning()) {
            animatorAll.end();
        }
        resetTranslationY(feedLoadingFrame1Line2, feedLoadingFrame1Line1,
                feedLoadingFrame1InRect,
                feedLoadingFrame1InLine3, feedLoadingFrame1InLine2, feedLoadingFrame1InLine1);
    }

    private void resetTranslationY(View... views) {
        for (View view : views) {
            ViewHelper.setTranslationY(view, 0);
        }
    }

    public void start() {
        stop();
        animatorAll = new AnimatorSet();
//        animatorAll.playSequentially(firstFrame(), secondFrame());
        animatorAll.playSequentially(firstFrame());

        animatorAll.addListener(new Animator.AnimatorListener() {
            @Override
            public void onAnimationStart(Animator animation) {

            }

            @Override
            public void onAnimationEnd(Animator animation) {
                animatorAll.setupStartValues();
                animatorAll.start();
            }

            @Override
            public void onAnimationCancel(Animator animation) {

            }

            @Override
            public void onAnimationRepeat(Animator animation) {

            }
        });
        animatorAll.start();
    }

    private static final int DURATION = 300;
    private static final int ITEM_INTERNAL = 100;
    private static final int FRAME_INTERNAL = 500;

    private AnimatorSet createAnimatorSet(boolean inOrOut, View... views) {
        AnimatorSet animatorSet = new AnimatorSet();
        List<Animator> animatorList = new LinkedList<>();
        int index = 0;
        for (View view : views) {
            Animator animator = inOrOut ? createInAnimator(view) : createOutAnimator(view);
            animator.setDuration(DURATION);
            animator.setStartDelay(ITEM_INTERNAL * index);
            animatorList.add(animator);
            index++;
        }
        animatorSet.playTogether(animatorList);
        return animatorSet;
    }

    private Animator createInAnimator(final View view) {
        view.setVisibility(View.INVISIBLE);
        Animator animator = ObjectAnimator.ofFloat(view, "translationY", -getScrollRange(), 0);
        animator.addListener(new Animator.AnimatorListener() {
            @Override
            public void onAnimationStart(Animator animation) {
                view.setVisibility(View.VISIBLE);
            }

            @Override
            public void onAnimationEnd(Animator animation) {

            }

            @Override
            public void onAnimationCancel(Animator animation) {

            }

            @Override
            public void onAnimationRepeat(Animator animation) {

            }
        });
        return animator;
    }

    private Animator createOutAnimator(final View view) {
        Animator animator = ObjectAnimator.ofFloat(view, "translationY", 0, getScrollRange());
        animator.addListener(new Animator.AnimatorListener() {
            @Override
            public void onAnimationStart(Animator animation) {

            }

            @Override
            public void onAnimationEnd(Animator animation) {
                view.setVisibility(View.INVISIBLE);
            }

            @Override
            public void onAnimationCancel(Animator animation) {

            }

            @Override
            public void onAnimationRepeat(Animator animation) {

            }
        });
        return animator;
    }

    private int getScrollRange() {
        return getMeasuredHeight();
    }
}
```


### 步骤分析

1. 在listener的onAnimationEnd方法里调用start,开启下一次动画
2. AnimatorSet的onAnimationEnd方法后，把mStarted设置为false
3. 调用AnimatorSet的end,由于mStarted为false，所以不会真正停止正在执行的动画
4. 由于调用了end方法，mTerminated 被设置为 true，所以这次的动画执行完后，不会执行回调函数
5. 最终导致在调用stop方法时恢复的坐标，都被执行的动画覆盖了
6. 即使在listener的onAnimationEnd方法里判断动画已经停止了，重新恢复坐标也没用，因为最后一次的回调不会执行


```java
AnimatorSet.java

public void onAnimationEnd(Animator animation) {
    animation.removeListener(this);
    mPlayingSet.remove(animation);
    Node animNode = mAnimatorSet.mNodeMap.get(animation);
    animNode.done = true;
    if (!mTerminated) {
        // Listeners are already notified of the AnimatorSet ending in cancel() or
        // end(); the logic below only kicks in when animations end normally
        ArrayList<Node> sortedNodes = mAnimatorSet.mSortedNodes;
        boolean allDone = true;
        int numSortedNodes = sortedNodes.size();
        for (int i = 0; i < numSortedNodes; ++i) {
            if (!sortedNodes.get(i).done) {
                allDone = false;
                break;
            }
        }
        if (allDone) {
            // If this was the last child animation to end, then notify listeners that this
            // AnimatorSet has ended
            if (mListeners != null) {
                ArrayList<AnimatorListener> tmpListeners =
                        (ArrayList<AnimatorListener>) mListeners.clone();
                int numListeners = tmpListeners.size();
                for (int i = 0; i < numListeners; ++i) {
                    tmpListeners.get(i).onAnimationEnd(mAnimatorSet);
                }
            }
            mAnimatorSet.mStarted = false;
        }
    }
}
        
public void end() {
    mTerminated = true;
    if (isStarted()) {
        if (mSortedNodes.size() != mNodes.size()) {
            // hasn't been started yet - sort the nodes now, then end them
            sortNodes();
            for (Node node : mSortedNodes) {
                if (mSetListener == null) {
                    mSetListener = new AnimatorSetListener(this);
                }
                node.animation.addListener(mSetListener);
            }
        }
        if (mDelayAnim != null) {
            mDelayAnim.cancel();
        }
        if (mSortedNodes.size() > 0) {
            for (Node node : mSortedNodes) {
                node.animation.end();
            }
        }
        if (mListeners != null) {
            ArrayList<AnimatorListener> tmpListeners =
                    (ArrayList<AnimatorListener>) mListeners.clone();
            for (AnimatorListener listener : tmpListeners) {
                listener.onAnimationEnd(this);
            }
        }
        mStarted = false;
    }
}        
        
```

### 解决方法
在listener的onAnimationEnd方法里通过handler post一下再重新开启下一次动画