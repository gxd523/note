[TOC]

## 绘制流程
![](https://gitee.com/hysbtr/pic/raw/master/view_flow_chat.png)

## Measure
* 测量View大小，最终需调用`setMeasuredDimension()`设置宽高
* 如果是ViewGroup，还需调用`measureChildren()`或`measureChild()`
* measureChildren()：测量所有子View
* measureChild()：测量指定View
* getWidth()：在`layout()`后，设置了`mRight`、`mLeft`才有值的
* getMeasureWidth()：在`measure()`后，调用了`setMeasuredDimension()`，也就是设置了`mMeasuredWidth`，才值了
* 从子View的Params转化为MeasureSpec开始说起

### MeasureSpecs
模式 | 二进制 | 描述
:---: | :---: | :---:
UNSPECIFIED | 00 | 表示父容器不对View有任何限制，一般用于系统内部，表示一种测量状态 
EXACTLY | 01 | 父容器已经检测出view所需的精确大小，相当于match_parent或指定具体数值 
AT_MOST | 10 | 表示子View不超过父View大小，相当于wrap_parent 

### ViewGroup.getChildMeasureSpec()
* 对于`DecorView`而言，它的`MeasureSpec`由窗口尺寸和其自身的`LayoutParams`共同决定
* 对于普通的`View`，它的`MeasureSpec`由父View的`MeasureSpec`的Mode和其自身的`LayoutParams`共同决定
* 但子View如果是ImageView这样，有内容可包裹的(src图片)，情况又会不一样
子View\父Vie     | EXACTLY | AT_MOST | UNSPECIFIED
:---: | :---: | :---: | ----- 
具体数值(px) | EXACTLY + childSize | EXACTLY + childSize | EXACTLY + childSize
match_parent | EXACTLY + parentSize | AT_MOST + parentSize | UNSPECIFIED + 0
wrap_content | AT_MOST +parentSize | AT_MOST + parentSize | UNSPECIFIED + 0

### 准确获取View宽高的时机
* `onWindowFocusChanged()`：
* `View.post()`：`attachInfo.mHandler`是在处理绘制`Message`里通过`View.dispatchAttachedToWindow()`传递给`View`的
* `ViewTreeObserver`：`dispatchOnGlobalLayout()`是在处理绘制`Message`里面调用的

## Layout
* 摆放View，通常由ViewGroup实现，View不需要
* onSizeChanged()：在`layout()`中调用`setFrame()`在调用`sizeChange()`

## Draw
* `onDraw()`：绘制当前View的内容
* dispatchDraw()：`onDraw()`后调用，里面调用子View的`draw()`
* ViewGroup默认不执行`onDraw()`，如果复写，需调用`setWillNotDraw(false)`清楚不需要绘制的标记

### 绘制过程
1. 对View的背景进行绘制：`drawBackground(canvas)`
2. 保存当前的图层信息(可跳过)
3. 绘制View的内容：`onDraw(canvas)`
4. 对View的子View进行绘制：`dispatchDraw(canvas)`
5. 绘制View的褪色的边缘，类似于阴影效果(可跳过)
6. 绘制View的装饰（例如：滚动条）

## invalidate()
* 关闭硬件加速的情况：
	* `parent.invalidateChild()`
	* `parent.invalidateChildInParent()`计算出`dirtyRect`(控件区域)逐级向上
	* 最后到`ViewRootImpl.invalidateChildInParent()`
	* `ViewRootImpl.scheduleTraversals()`
	* 再到`Choreographer.postCallback()`
	* `ViewRootImpl.performTraversals()`
	* `ViewRootImpl.drawSoftware()`
* 开启硬件加速的情况：
	* `parent.invalidateChild()`
	* `parent.onDescendantInvalidated()`
	* `viewRootImpl.onDescendantInvalidated()`
	* `dirtyRect`(全屏区域)`ViewRootImpl.scheduleTraversals()`
	* 再到`Choreographer.postCallback()`
	* `ViewRootImpl.performTraversals()`
	* `mAttachInfo.mThreadedRenderer.draw(mView)`
* 之前做完测量、绘制`mLayoutRequested`置为`false`，所以一定不会走测量、绘制
* postInvalidate()：一般在非UI线程调用，用Handler发送异步消息，`handleMessage()`中调用`invalidate()`

## requestLayout()
* `mParent.requestLayout()`
* `viewRootImpl.requestLayout()`，`mLayoutRequested`置为`true`
* 所以一定触发测量、绘制
* 也执行到了`viewRootImpl.draw()`，但`dirtyRect`没有区域，所以没有走进绘制里面