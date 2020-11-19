---
title: 《Android开发艺术探索》——View事件分发机制
tags:
  - View事件
  - 分发机制
categories: Android
abbrlink: b516dca1
date: 2017-03-14 16:42:00
---
# 概念
**同一个事件序列**指的是从手指触摸屏幕的那一刻开始，到手指离开屏幕的那一刻结束，在这个过程产生的一系列事件。以down事件开始，可能经过n多个move事件，最终以up事件结束。

# 案例
自定义三个布局
```
<com.yolo.myapplication.view.FirstFrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_view_demo"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/holo_blue_bright"
    tools:context="com.yolo.myapplication.view.ViewDemoActivity">

    <com.yolo.myapplication.view.SecondRelativeLayout
        android:layout_width="300dp"
        android:layout_height="300dp"
        android:layout_gravity="center"
        android:background="@android:color/holo_green_dark">

        <com.yolo.myapplication.view.ThirdTextView
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:clickable="true"
            android:background="@android:color/holo_red_light"
            android:layout_centerInParent="true" />

    </com.yolo.myapplication.view.SecondRelativeLayout>


</com.yolo.myapplication.view.FirstFrameLayout>
```
<!-- more -->
![布局.png](93730-56415ba0c6f300e0.webp)
# 情况一
默认情况，未设置返回true或false，事件都是return super.onXXX(event);
```
I/Activity: dispatchTouchEvent>>DOWN
I/FirstFrameLayout: dispatchTouchEvent>>DOWN
I/FirstFrameLayout: onInterceptTouchEvent>>>>DOWN
I/SecondRelativeLayout: dispatchTouchEvent>>DOWN
I/SecondRelativeLayout: onInterceptTouchEvent>>>>DOWN
I/ThirdTextView: dispatchTouchEvent>>DOWN
I/ThirdTextView: onTouchEvent>>>>DOWN
I/SecondRelativeLayout: onTouchEvent>>>>DOWN
I/FirstFrameLayout: onTouchEvent>>>>DOWN
I/Activity: dispatchTouchEvent>>UP
```
可见，事件默认回传到Activity。

# 情况二
ThirdTextView的onTouchEvent返回true。或者设置ThirdTextView的属性clickable=true
日志：
```
I/FirstFrameLayout:     dispatchTouchEvent>>DOWN
I/FirstFrameLayout:     onInterceptTouchEvent>>>>DOWN
I/SecondRelativeLayout: dispatchTouchEvent>>DOWN
I/SecondRelativeLayout: onInterceptTouchEvent>>>>DOWN
I/ThirdTextView:        dispatchTouchEvent>>DOWN
I/ThirdTextView:        onTouchEvent>>>>DOWN
I/FirstFrameLayout:     dispatchTouchEvent>>UP
I/FirstFrameLayout:     onInterceptTouchEvent>>>>UP
I/SecondRelativeLayout: dispatchTouchEvent>>UP
I/SecondRelativeLayout: onInterceptTouchEvent>>>>UP
I/ThirdTextView:        dispatchTouchEvent>>UP
I/ThirdTextView:        onTouchEvent>>>>UP
```
图例：
![默认情况下事件传递机制](93730-e9f90388dd8511be.webp)

- 结论：

  >View的OnTouchEvent的事件默认都会消耗事件（返回true），除非它是不可点击的（clickable和longClickable同时为false）。如果View是不可点击的（clickable和longClickable同时为false），则返回false。

- 注意：
View的longClickable默认都为false，clickable属性分情况：如Button的clickable属性默认为true，TextView的clickable属性默认为false。
View的enable属性不影响onTouchEvent默认返回值。clickable和longClickable属性会影响。

#情况三
FristFrameLayout的onTouchEvent返回true，SecondRelativeLayout和ThirdTextView的OnTouchEvent返回false。
```
I/FirstFrameLayout:     dispatchTouchEvent>>DOWN
I/FirstFrameLayout:     onInterceptTouchEvent>>>>DOWN
I/SecondRelativeLayout: dispatchTouchEvent>>DOWN
I/SecondRelativeLayout: onInterceptTouchEvent>>>>DOWN
I/ThirdTextView:        dispatchTouchEvent>>DOWN
I/ThirdTextView:        onTouchEvent>>>>DOWN
I/SecondRelativeLayout: onTouchEvent>>>>DOWN
I/FirstFrameLayout:     onTouchEvent>>>>DOWN
I/FirstFrameLayout:     dispatchTouchEvent>>UP
I/FirstFrameLayout:     onTouchEvent>>>>UP
```
图例：
![View事件传递](93730-3ee243cc1290c160.webp)

- 结论：
> 如果View一旦开始处理事件，如果它不消费ACTION_DOWN事件，那么同一个事件序列的其他事件也不会再交给它，并且重新交给它的父元素去处理。例如红线的down事件流向。

# 情况四
SecondRelativeLayout 拦截事件，onInterceptTouchEvent返回true，且onTouchEvent返回true
```
I/FirstFrameLayout:     dispatchTouchEvent>>DOWN
I/FirstFrameLayout:     onInterceptTouchEvent>>>>DOWN
I/SecondRelativeLayout: dispatchTouchEvent>>DOWN
I/SecondRelativeLayout: onInterceptTouchEvent>>>>DOWN
I/SecondRelativeLayout: onTouchEvent>>>>DOWN
I/FirstFrameLayout:     dispatchTouchEvent>>UP
I/FirstFrameLayout:     onInterceptTouchEvent>>>>UP
I/SecondRelativeLayout: dispatchTouchEvent>>UP
I/SecondRelativeLayout: onTouchEvent>>>>UP
```
![View事件传递机制](93730-b60b8e7b97033c31.webp)

- 结论:

> 正常情况下，一个事件序列只能被一个View拦截且消耗。因为一旦一个元素拦截了此事件，那么同一事件序列内的所有事件都会**直接**交给它处理。<p>
> View一旦决定拦截，那么事件序列只能由它来处理，并且它的onInterceptTouchEvent不会被调用。比如SecondRelativeLayout拦截了Down事件，那么事件序列中的Up直接交给它处理，并且不会再去执行onInterceptTouchEvent。

- 注意：
事件传递由外向内，即事件总是先传递到父元素，然后再由父元素分发给子View，通过`requestDisallowInterceptTouchEvent`方法可以在子元素中干预父元素的事件分发过程。但是ACTION_DOWN事件除外。

# 注意
- 当一个View需要处理事件时，如果它设置了OnTouchListener，那么这个接口的onTouch方法会被回调。如果onTouch方法返回true，则onTouchEvent不会被调用。因此OnTouchListener优先级高于onTouchEvent。
> OnTouchListener  >  onTouchEvent  > OnClickListener


- View拦截了事件，那么事件序列中的其余事件则直接跳过onInterceptTouchEvent方法去执行onTouchEvent方法。是因为mFirstTouchTarget ！= null 的标记。
```
// Handle an initial down.
if (actionMasked == MotionEvent.ACTION_DOWN) {
	// Throw away all previous state when starting a new touch gesture.
	// The framework may have dropped the up or cancel event for the previous gesture
	// due to an app switch, ANR, or some other state change.
	cancelAndClearTouchTargets(ev);
	resetTouchState();
}
final boolean intercepted;
//如果子View处理了事件，mFirstTouchTarget != null 成立，为true。
//且此时传递的是事件序列的其余事件，不是Down事件，因此整个条件不成立。
//不会执行onInterceptTouchEvent
if (actionMasked == MotionEvent.ACTION_DOWN
		|| mFirstTouchTarget != null) {
	final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
	if (!disallowIntercept) {
		intercepted = onInterceptTouchEvent(ev);
		ev.setAction(action); // restore action in case it was changed
	} else {
		intercepted = false;
	}
} else {
	// There are no touch targets and this action is not an initial down
	// so this view group continues to intercept touches.
	intercepted = true;
}
```

- FLAG_DISALLOW_INTERCEPT标记位。
这个标记位可以通过requestDisallowInterceptTouchEvent方法来设置。一般用于子View中，设置后，ViewGroup将无法拦截除了ACTION_DOWN以外的事件。

  ViewGroup在分发事件的时候，会重置此标记位。将导致子View设置这个标记位无效。

# 滑动冲突解决
一般采用相对简单的外部拦截法进行处理。
伪代码：
```
@Override
public boolean onInterceptTouchEvent(MotionEvent event) {
	boolean intercepted = false;
	int x = (int) event.getX();
	int y = (int) event.getY();

	switch (event.getAction()) {
	case MotionEvent.ACTION_DOWN: {
		intercepted = false;
        //TODO
		break;
	}
	case MotionEvent.ACTION_MOVE: {
		int deltaX = x - mLastXIntercept;
		int deltaY = y - mLastYIntercept;
        //拦截条件：如果X轴偏移量大于Y轴，则表示水平滑动，进行拦截处理。
		if (Math.abs(deltaX) > Math.abs(deltaY)) {
			intercepted = true;
		} else {
			intercepted = false;
		}
		break;
	}
	case MotionEvent.ACTION_UP: {
		intercepted = false;
		break;
	}
	default:
		break;
	}
	mLastXIntercept = x;
	mLastYIntercept = y;

	return intercepted;
}
```