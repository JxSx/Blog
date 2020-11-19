---
title: 《Android开发艺术探索》——View的事件体系
tags:
  - View
  - Scroller
categories: Android
abbrlink: 236631f
date: 2017-03-13 16:42:00
---
# View的事件体系
-  自定义控件
-   滑动冲突解决
## 3.1View基础知识
1. View的位置参数
2. MotionEvent和TouchSlop对象
3. VelocityTracker
4. GestureDetector和Scroller对象
### 3.1.1 什么是View
View是Android中所有控件的基类
<!-- more -->
### 3.1.2 View的位置参数
![图片](http://www.wfuyu.com/uploadfile/cj/20150117/20150115155321445.png)
### 3.1.3 MotionEvent和TouchSlop
#### 1.MotionEvent
Touch事件中，典型的事件有如下几种：
- ACTION_DOWN —— 手指接触屏幕
- ACTION_MOVE —— 手指在屏幕上移动
- ACTION_UP   —— 手指从屏幕离开
#### 2.TouchSlop
TouchSlop是系统所能识别出的被认为是滑动的最小距离，换句话说，当手指在屏幕上滑动时，当两次滑动之间的距离小于这个常量，那么系统不认为是在进行滑动操作。通过以下方式获取这个常量值：
```
ViewConfiguration.get(getContext()).getScaledTouchSlop();
```
这个常量有神马意义呢？
可以使用这个常量判断是否达到滑动条件，处理滑动时，做一些过滤。
### 3.1.4 VelocityTracker、GestureDetector和Scroller对象
#### 1. VelocityTracker
速度追踪，用于追踪手指在滑动过程中的速度，包括水平和竖直方向的速度。
1. 构造方法中初始化获取VelocityTracker对象
```
VelocityTracker mVelocityTracker = VelocityTracker.obtain();
```
2. 在**onTouchEvent**方法中添加追踪事件
```
mVelocityTracker.addMovement(event);
```
3. 在**ACTION_UP**事件中获取当前的速度。注意这里计算的是1000ms时间间隔移动的像素值，假设像素是100，即速度是每秒100像素。手指从右向左滑动，速度为负值。
```
mVelocityTracker.computeCurrentVelocity(1000);
float xVelocity = mVelocityTracker.getXVelocity();
float yVelocity = mVelocityTracker.getYVelocity();
```
4. 最后，当不需要它的时候需要调用**clear**方法来重置并回收内存。
```
mVelocityTracker.clear();
mVelocityTracker.recycle();
```
#### 2.GestureDetecor
手势检测，用于辅助检测用户的单击、滑动、长按、双击等行为。
1. 创建一个**GestureDetecor**对象并实现**OnGestureListener**接口，根据需要实现单击等方法：
```
GestureDetector mGestureDetector = new GestureDetector(this);
// 解决长按屏幕后无法拖动的现象
mGestureDetector.setIsLongpressEnabled(false);
```
2. 接管目标**View**的**onTouchEvent**方法，在待监听**View**的**onTouchEvent**方法中添加如下实现：
```
boolean consume = mGestureDetector.onTouchEvent(event);
return consume;
```
**建议**：
	如果只是监听滑动操作，建议在onTouchEvent中实现；如果要监听双击这种行为，则使用GestureDetector 。
#### 3.Scroller
弹性滑动对象，用于实现View的弹性滑动。
View的scrollTo/scrollBy方法来滑动时，过程是瞬间完成的。使用Scroller则有过渡滑动的效果。注意，Scoller本身无法让View弹性滑动，它需要和View的computerScroller方法配合使用。
1. 构造方法初始化
```
Scroller mScroller = new Scroller(getContext());
```
2. 缓慢滑动到指定位置，一般在ACTION_UP 方法中执行，松手回弹效果。
```
private void smoothScrollBy(int dx, int dy) {
    mScroller.startScroll(getScrollX(), 0, dx, 0, 500);
    invalidate();
}

@Override
public void computeScroll() {
    if (mScroller.computeScrollOffset()) {
        scrollTo(mScroller.getCurrX(), mScroller.getCurrY());
        postInvalidate();
    }
}
```
**Scroller原理**
![原理图](http://www.2cto.com/uploadfile/Collfiles/20140810/2014081009245012.jpg)
参考链接：
* http://www.2cto.com/kf/201408/324683.html
* http://androidxref.com/2.3.6/xref
* http://blog.csdn.net/wangjinyu501/article/details/32339379
* http://blog.csdn.net/zjmdp/article/details/7713209
* http://blog.csdn.net/xiaanming/article/details/17483273

